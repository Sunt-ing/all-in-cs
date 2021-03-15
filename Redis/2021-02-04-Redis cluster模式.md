### 基本通信原理

每个Redis开放两个端口号，一个用来提供服务，另一个+1万的端口号用来和cluster中其他实例交流。

cluster采用了分散式的集群元信息维护。和ZooKeeper等集中式方式相比，它实时性更差，但是元数据存储和更新的压力更小。

### Gossip 协议

gossip 协议包含多种消息，包含 `ping` , `pong` , `meet` , `fail` 等等。

### 分布式寻址算法

##### hash 算法（大量缓存重建）

##### 一致性 hash 算法（自动缓存迁移）+ 虚拟节点（自动负载均衡）

一致性hash在节点数量太少时，会导致缓存热点，所以可以对一个节点算出多个虚拟节点

##### Redis cluster 的 hash slot 算法

每个key的hash值对16384取余，得到hash slot的位置。node数量变化时，可以移动一些hash slot的位置。移动hash slot的成本很低。

### Redis cluster 的高可用与主备切换原理

如果半数节点认为node宕机了，那么node便下线。然后找一个有资格成为master的slave作为新的master。

slave按照offset大小先后被选举：如果有半数以上的master节点认为某个slave可以作为master，那么这个slave便成为了master。