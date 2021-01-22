下载 maven：http://mirror.bit.edu.cn/apache/maven/maven-3/

```shell
# 使用wget命令在线下载，现在前必须确保wget已安装，否则yum install wget
wget https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.2.5/binaries/apache-maven-3.2.5-bin.tar.gz
# 或者用这个地址
wget http://mirror.bit.edu.cn/apache/maven/maven-3/3.2.5/binaries/apache-maven-3.2.5-bin.tar.gz
# 解压
tar -zxvf apache-maven-3.2.5-bin.tar.gz
# 移动
mv apache-maven-3.2.5 /usr/local/maven3.2.5
```

设置环境变量

```shell
vi /etc/profile
```

在 /etc/profile 中添加以下几行：

```shell
export MAVEN_HOME=/usr/local/maven3
export PATH=$MAVEN_HOME/bin:$PATH
```

执行 source /etc/profile 使环境变量生效：

```shell
source /etc/profile
```

最后运行 mvn -v 验证 maven 是否安装成功，如果安装成功会打印如下内容：

```
mvn -v
```

打开 /usr/local/maven3/conf/setting.xml 文件，将maven默认的仓库设置我们的仓库路径：

```xml
<!-- localRepository  
  | The path to the local repository maven will use to store artifacts.  
  |  
  | Default: ${user.home}/.m2/repository  
 <localRepository>/path/to/local/repo</localRepository>  
 -->  
 <localRepository>~/.m2/repository</localRepository>
```

配置镜像

```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
</mirror> 
<mirror>
    <id>jboss-public-repository-group</id>
    <mirrorOf>central</mirrorOf>
    <name>JBoss Public Repository Group</name>
    <url>http://repository.jboss.org/nexus/content/groups/public</url>
</mirror>
<mirror>
    <id>ibiblio</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://mirrors.ibiblio.org/pub/mirrors/maven2/</url>
</mirror>
<mirror>
    <id>central</id>
    <name>Maven Repository Switchboard</name>
    <url>http://repo1.maven.org/maven2/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
<mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
</mirror>
<mirror>
    <id>nexus-pentaho</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus pentaho</name>
    <url>https://nexus.pentaho.org/content/repositories/omni/</url>
</mirror>
```



#### 参考

- https://blog.csdn.net/weixx3/article/details/80331538

- https://www.cnblogs.com/myitnews/p/11492967.html

