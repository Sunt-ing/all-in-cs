#### spark优势

- 机器学习整合 
- 生态丰富：除了 Streaming 还有      SQL、MLib、Graphx 等
- 对 Kubernetes 云原生技术的原生支持更加到位

#### Flink优势

- 流处理（这也是大家在      2016 年开始关注 Flink 的原因）
- Flink 原来用 Java 写确实很难看，现在学 Spark 支持 Scala
- 最初对 SQL 支持的就更好。Spark 在 SQL 上的优化，尤其是 DataFrame 到 DataSet 其实是借鉴的 Flink 的 

#### cache in memory 决定权

- flink：由框架判断，因为 Flink 对数据的处理不像 Spark 以 RDD 为单位，而是更细粒度，对内存的规划更好
- Spark：用户来指定

#### 流/批

- 数据抽象

  - 批

    - Spark：RDD。RDD 在 Spark 中运行时是表现为 java        objects 的。通过引入 Tungsten，这块有了些许的改变
    - Flink：DataSet。Flink 中Dataset被表现为 logical plan

  - 流

    - Spark：DStream，不过内部实际还是        RDD 
    - Flink：DataStreams

- 模拟

  - 在 Spark 中，Dstream 和 Dataframe 都是基于 RDD 抽象的
  - Spark 把 streaming 看成是更快的批处理，但是 mini batch 近似 Stream 会搞乱 batch 里的顺序
  - Flink 把批处理看成 streaming 的 special case，Stream 近似 Batch 没有硬伤

- 发展

  - Flink 中 Dataset 和       DataStream 是同一引擎之上两个独立的抽象。Flink 在朝让二者的行为合并在一起这个方向努力 

  - Spark 在流计算方面的努力

    - Spark 1.6 引入了 dataset API ，也许最终会取代 RDD 抽象
    - 从 2.0 开始引入了 Structured        Streaming，重新整理流计算的语义，使用微批（Micro-Batch）处理执行模型，支持按事件时间处理和端到端的一致性
    - 在 2.3 实现了 初始的持续处理（Continuous        Processing）
    - 根据官方介绍，Spark 执行引擎未来的发展会和 Flink 类似

#### 内存管理

- Spark1.5 以前都是用 java 的内存管理来做数据缓存，容易导致 OOM 或者 gc
- Flink 除了把数据存在自己管理的内存以外，还直接操作二进制数据，启发了Spark
- Spark1.5 以后使用 tungsten 来精确控制内存 

#### API

- 非常相像 

#### 对 windowing 的支持

- 因为 Spark 的小批量机制，Spark 对于 windowing 的支持非常有限

- - 只能基于 process time，且只能对 batches 来做 window

- Flink 允许基于 process time,data      time,record 来做 windowing

- 底层

  - Spark 对待每个时间窗口就像对待文件，只不过这些文件允许放一部分或者全部在内存里，在内部实现是对不可变数据集的操作，所有操作都是基于 scan

- 优点是只要基于 map reduce 封装出来的算子在      streaming 上基本上都可以用

- 缺点是 Spark 里 RDD      的生成和消费成本太高，没法做到毫秒级，秒级相对来说 rdd 自身开销也占了不小，但是如果是分钟级，小时级，rdd 自身开销相对来说不大了，这个时候      spark 的吞吐量优势就出来了

- 缺点

  - 因为是基于状态的计算，所以在几个窗口内做做排序什么的很难实现，只能把所有状态丢到内存里你自己做实现，超出内存了估计就直接       oom 了
  - spark 可以利用 rdd       的优势，哪怕数据超出内存一样算，所以在较粗时间粒度极限吞吐量上 spark streaming 要优于 flink

- spark streaming 提供的      reduceByWindow 函数支持一个 inverse reduce 函数，比如你计算最近 1 小时，按秒级别窗口滑动，spark 通过实现      inverse reduce 函数每次只计算进来和要逐出的子窗口

- flink 没提供任何这种相关的 api，你就必须要执行      3600 个窗口的聚合操作，当然这个通过自己封装聚合算子还是能实现的

- Flink 通过计算窗口帮助修正数据计算的方向，从而获得成功

#### Iterative processing

- Spark 可以利用内存 cache 来加速机器学习算法
- 但是大部分机器学习算法其实是一个有环的数据流，但是在      Spark 中，实际是用无环图来表示的
- Flink 支持在 runtime      中的有环数据流，这样表示机器学习算法更有效率