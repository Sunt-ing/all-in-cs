## 生产环境中Redis的部署

Redis cluster，10台机器，5主5从，每个节点峰值QPS可以达到5万，5台的峰值是25万QPS。

配置：32G 内存 + 8 核 CPU + 1T 磁盘，分配给 Redis 进程的是 10g 内存，一般线上生产环境，Redis 的内存尽量不要超过 10g，超过 10g 可能会有问题。

写入的数据：商品数据，每条10kb，10000条就是1G。常驻内存有20G，有200万条数据，占总内存的不到一半。目前高峰期QPS是 3500。



## 前期准备

### 下载编译

下载Redis，我选的是5.0.5版本的，Redis 3.0以上的版本才有Cluster模式。然后解压，并进入目录进行编译：

```shell
flink@dbg16:~$ wget http://download.redis.io/releases/redis-5.0.5.tar.gz
flink@dbg16:~$ tar zxvf redis-5.0.5.tar.gz
flink@dbg16:~$ cd redis-5.0.5
flink@dbg16:~/redis-5.0.5$ make
```

### 修改配置文件

由于redis cluster中 master 选举使用的是多数投票法，所以使用redis cluster至少需要三个master node，一般为了数据安全和服务不中断，至少使用 6 个 node (3 master + 3 slave)。由于我们有5台机器，同时为了方便以后在单机上使用伪分布式，我建立了5*2=10个配置文件，它们的差别在于使用的端口号不同。原则上来说，不同机器上的redis instance的端口号可以相同。但是为了方便，我们使用不同的端口号：7000，7001，7002，7003，7004，7005，7006，7007，7008，7009。

默认的配置文件在 `redis-5.0.5/redis.conf`，我们使用的配置文件包括：redis7000.conf等。

我们的配置如下（以7000端口的机器为例）：

```yaml
port 7000 # 每个node 的client port
cluster-config-file nodes_7000.conf # 每个node 需要独立，cluster 自行维护使用，不需人为介入
cluster-enabled yes 
cluster-node-timeout 5000 
appendonly yes 
daemonize yes 
protected-mode no
```



## 日常运维

### 启动redis cluster集群

分别到不同的机器上，通过配置文件逐一启动Redis instance：

```shell
flink@dbg16:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7000.conf
flink@dbg16:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7001.conf

flink@dbg17:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7002.conf
flink@dbg17:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7003.conf

flink@dbg18:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7004.conf
flink@dbg18:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7005.conf

flink@dbg19:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7006.conf
flink@dbg19:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7007.conf

flink@dbg20:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7008.conf
flink@dbg20:~$ ~/redis-5.0.5/src/redis-server ~/redis-5.0.5/redis7009.conf
```

然后创建整个集群：

```shell
flink@dbg16:~$ ~/redis-5.0.5/src/redis-cli --cluster create 10.20.1.196:7000 10.20.1.196:7001 10.20.56.41:7002 10.20.56.41:7003 10.20.98.34:7004 10.20.98.34:7005 10.20.95.197:7006 10.20.95.197:7007 10.20.120.63:7008 10.20.120.63:7009 --cluster-replicas 1
```

### 关闭redis cluster集群

直接执行关闭脚本：~/shutdown-redis-cluster.sh

```shell
~/redis-5.0.5/src/redis-cli -c -h 10.20.1.196 -p 7000  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 10.20.1.196 -p 7001  -a fatsnake shutdown

~/redis-5.0.5/src/redis-cli -c -h 10.20.56.41 -p 7002  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 10.20.56.41 -p 7003  -a fatsnake shutdown

~/redis-5.0.5/src/redis-cli -c -h 10.20.98.34 -p 7004  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 10.20.98.34 -p 7005  -a fatsnake shutdown

~/redis-5.0.5/src/redis-cli -c -h 10.20.95.197 -p 7006  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 10.20.95.197 -p 7007  -a fatsnake shutdown

~/redis-5.0.5/src/redis-cli -c -h 10.20.120.63 -p 7008  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 10.20.120.63 -p 7009  -a fatsnake shutdown
```

其实也可以直接kill -9，但是不推荐。

### 启动redis cluster伪分布式集群

有时，我们会在本地建立伪分布式集群，以方便地进行各种测试。Redis伪分布式集群同样是分别启动，然后加入集群中：

```shell
redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 --cluster-replicas 1
```

### 关闭redis cluster伪分布式集群

直接执行关闭脚本：~/shutdown-redis-pseudo-distributed.sh

```shell
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7000  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7001  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7002  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7003  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7004  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7005  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7006  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7007  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7008  -a fatsnake shutdown
~/redis-5.0.5/src/redis-cli -c -h 127.0.0.1 -p 7009  -a fatsnake shutdown
```

### redis cli 命令

##### 连接

可以使用两种方式连接 redis 服务器。

- 第一种：交互式方式：redis-cli -c -h {host} -p {port}  方式连接，其中 -c 表示连接集群

- 第二种方式：命令方式：redis-cli -c -h {host} -p {port} {command} 直接得到命令的返回结果

如果要在Java中使用Redis，可能得用Jedis、Redisson等Redis的Java客户端

##### 查看Redis node

命令：cluster nodes



## 关于 Redis Cluster

- 仅适合于 Redis3.0（包括 3.0）以上版本
- 每个 redis cluster 有 16384 个 hash slot，key 经过 CRC16 计算后决定储存的 cluster node
- 每个 redis node 都需要开放两个 TCP port：一个是 redis client 连线使用的 port (常见为`6379`) 另一个是 cluster bus 用来做 node 间的交互沟通 (固定为 client port 加上`10000`)
- 仅支持单一 database
- 不支持多个 key 的操作 (因为可能需要跨 node)



### Reference

- https://www.jianshu.com/p/3f00762f639c
- https://blog.csdn.net/justry_deng/article/details/89205155
- https://www.cnblogs.com/kongzhongqijing/p/6867960.html
- https://blog.yowko.com/create-redis-cluster/