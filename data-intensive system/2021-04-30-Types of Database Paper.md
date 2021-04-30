![image-20210430144103806](2021-04-30-Types%20of%20Database%20Paper.assets/image-20210430144103806.png)



## Types of Database Paper

今天看见基哥在群里发了一张图，名字就叫《Types of Database Paper》，我没Google到相关的解读或者资料，所以自己来对图中提到的各个方向做些解读。

#### ML is all you need to build a DBMS

这类论文近几年确实挺多的，这个属于MLSYS的一部分吧。据说一开始是Jeff Dean开搞的，后面各个平台都在做，包括Learned index之类的工作，我们实验室也有人在做。

#### Actually, reinforcement learning is the kind of ML you need for building a DBMS, like for real this time, LOL

别问，问就是All in MlSys。

#### My colleague is wrong and I can finally prove it

这类论文就是同一个实验室的人在一个方向上不断地搞，我见过的例子比如浙大的Dragoon论文里把之前自己发的Ultraman批判了一番。

#### The operating system is at it again

这个可能说的是各种资源调度相关的东西，像是YARN、Mesos、Kubernetes这样的调度系统，或者是对于Spark、Flink等系统内部自带的资源调度方式的改良。

#### We figured out how to optimize really complex SQL query, so email us if you need some

优化查询其实是一个经典问题了，做些谓词下推、基数估计之类的工作，近年来Learning-based cardinality estimation 也很常见，我们实验室就有人在做相关的工作。不过，我确实没怎么见过“so email us if you need some”之类的话。

#### What are NVM/SSD doing down there

探索Modern Hardware包括NVM/SSD/GPU等在数据库中可能的应用。这个我见过的包括周信静他们给基于NVM的存储引擎设计数据结构以弥补读写不平衡的现象，WiscKey改进传统LSM-Tree在SSD上的写放大问题等，使用GPU加速Hive、Flink、Spark等系统。我们实验室就有人在做相关的工作。

#### This task I had to anyway turned out to be hard enough for its own paper

这个其实还是挺普遍的，特别是在博士生和未获得终身教职的人中。很多人都在尝试造出自己能解决的但又有点难度的问题，而非尽力解决真正对数据库影响深远的问题。不过，要想在学术界干出有意义的工作，恐怕首先得survive，得设法留在学术界。

#### Hey, at least we showed that this method can produce results! That's not nothing, right?

这应该就是在说那些创新性不够强、结果不够好的文章。

#### Check out this weird trick one of us saw while using a KV store

不了解。我猜应该是在处理一些神奇的一致性问题？

#### We are 33 scientists and here's what we've conjured while chilling at a Seattle supper club

特指SIGMOD'19的文章《The Seattle Report on Database Research》。一伙大佬喝茶聊天，顺便整理了下聊天记录发了出来。

#### Some thoughts on how erveryone else is bad at research —— By Michael Stonebraker

Stonebraker经常对数据库领域指指点点，说这个不好那个不好，比如和David J. DeWitt 在《MapReduce: A major step backwards》一文中怒斥了一通MapReduce，当然这也为后续基于MapReduce范式的大数据系统的设计和发展指明了一些路，后续的结果也证实了MapReduce范式系统在全面学习关系型数据库。

#### We scanned some undergraduates

这个不会说的是采访了一些本科生对于某些系统的看法吧。





