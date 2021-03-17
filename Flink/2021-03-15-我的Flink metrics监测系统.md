## Flink的metrics机制

Flink在设计时就考虑到各种metrics的展示了，所以留下了一个优雅的metrics机制。这个机制提供了非常丰富的信息（见本文末尾）。

### metrics种类

Flink中含有四种metrics：

#### **Counter**

写过 mapreduce 作业的开发人员就应该很熟悉 Counter，其实含义都是一样的，就是对一个计数器进行累加，即对于多条数据和多兆数据一直往上加的过程。

#### **Gauge**

是最简单的 Metrics，它反映一个值。比如要看现在 Java heap 内存用了多少，就可以每次实时的暴露一个 Gauge，Gauge 当前的值就是 heap 使用的量。 

#### **Meter**

是指统计吞吐量和单位时间内发生 “事件” 的次数。它相当于求一种速率，即事件次数除以使用的时间。 

#### **Histogram**

比较复杂，也并不常用，Histogram 用于统计一些数据的分布，比如说 Quantile、Mean、StdDev、Max、Min 等。

同时，Flink允许用户在提交的作业的源码中实现接口，注册新metrics，所以基本上工业界在获取集群内部的metrics时很少更改Flink源码

### metrics获取方式

获得这些metrics的方式包括：

#### RESTful接口

- 描述：用户使用程序向管理节点（JobManager）发送相应的RESTful HTTP报文
- 原理：各个TaskManager节点（类似于Worker节点）会不断把信息汇报给JobManager，然后我们就可以向JobManager获取相应的信息了
- 缺点：JobManager的内存有限，无法存储大量的metrics信息，同时有一定的延迟；而且对我而言，需要写一个能发各种报文的Python脚本，会涉及到不少东西，有一定工作量

##### 使用举例

###### 获取job详情

方法：GET  /v1/jobs/ce793f18efab10127f0626a37ff4b4d4

```json
{
	jid: "ce793f18efab10127f0626a37ff4b4d4",
	name: "Test",
	isStoppable: false,
	state: "RUNNING",
	start - time: 1551577191874,
	end - time: -1,
	duration: 295120489,
	now: 1551872312363,
	timestamps: {
		CANCELLING: 0,
		FAILED: 0,
		RESTARTING: 1551577191874,
		SUSPENDED: 0,
		RUNNING: 1551577191896,
		CANCELED: 0,
		CREATED: 1551577191874,
		RECONCILING: 0,
		FINISHED: 0,
		SUSPENDING: 0,
		FAILING: 0
	},
	vertices: [{
			id: "cbc357ccb763df2852fee8c4fc7d55f2",
			name: "Source: Custom Source -> Flat Map",
			parallelism: 12,
			status: "RUNNING",
			start - time: 1551577260649,
			end - time: -1,
			duration: 295051714,
			tasks: {
				RUNNING: 12,
				RECONCILING: 0,
				SCHEDULED: 0,
				DEPLOYING: 0,
				CANCELED: 0,
				FAILED: 0,
				CREATED: 0,
				FINISHED: 0,
				CANCELING: 0
			},
			metrics: {
				read - bytes: 0,
				read - bytes - complete: true,
				write - bytes: 92306188,
				write - bytes - complete: true,
				read - records: 0,
				read - records - complete: true,
				write - records: 244844,
				write - records - complete: true
			}
		}, {
			id: "9dd63673dd41ea021b896d5203f3ba7c",
			name: "Sink: Unnamed",
			parallelism: 12,
			status: "RUNNING",
			start - time: 1551577260652,
			end - time: -1,
			duration: 295051711,
			tasks: {
				RUNNING: 12,
				RECONCILING: 0,
				SCHEDULED: 0,
				DEPLOYING: 0,
				CANCELED: 0,
				FAILED: 0,
				CREATED: 0,
				FINISHED: 0,
				CANCELING: 0
			},
			metrics: {
				read - bytes: 93938476,
				read - bytes - complete: true,
				write - bytes: 0,
				write - bytes - complete: true,
				read - records: 244844,
				read - records - complete: true,
				write - records: 0,
				write - records - complete: true
			}
		}
	],
	status - counts: {
		RUNNING: 2,
		RECONCILING: 0,
		SCHEDULED: 0,
		DEPLOYING: 0,
		CANCELED: 0,
		FAILED: 0,
		CREATED: 0,
		FINISHED: 0,
		CANCELING: 0
	},
	plan: {
		jid: "ce793f18efab10127f0626a37ff4b4d4",
		name: "Test",
		nodes: [{
				id: "9dd63673dd41ea021b896d5203f3ba7c",
				parallelism: 12,
				operator: "",
				operator_strategy: "",
				description: "Sink: Unnamed",
				inputs: [{
						num: 0,
						id: "cbc357ccb763df2852fee8c4fc7d55f2",
						ship_strategy: "FORWARD",
						exchange: "pipelined_bounded"
					}
				],
				optimizer_properties: {}
			}, {
				id: "cbc357ccb763df2852fee8c4fc7d55f2",
				parallelism: 12,
				operator: "",
				operator_strategy: "",
				description: "Source: Custom Source -&gt; Flat Map",
				optimizer_properties: {}
			}
		]
	}
}
```

该接口有 job 开始时间 / 结束时间 / 已经运行时长以及当前时间等。

我们这里需要的是 job 的运行顶点信息 (vertices) 或执行计划 (plan) 中的顶点 / 节点信息。

###### vertices 和 plan 的区别

plan 是执行计划，在 JobGraph 生成时就已经生成；vertices 是运行时的顶点，包含运行时节点的 metric 信息，**这里的 metric 信息比较局限**，一般只包含输入 / 输出元素总数和输入 / 输出字节总数。

###### 获取 Flink 的实时吞吐的方法

vertices的metric的输入/输出只是在 flink 的各个节点之间，不包含与外界组件的交互信息。所以，这里的统计里， flink source 的 read-bytes/read-records 都是 0；flink sink 的 write-bytes/write-records 都是 0。在 flink UI 上的显示也是如此。

我们获取到了各顶点 (vertices) 对应的 id ，分别是 Source: Custom Source -> Flat Map: cbc357ccb763df2852fee8c4fc7d55f2 (flink 将原来的两个 operator 通过 channing 优化合并成了一个，这里不再展开)，Sink: Unnamed: 9dd63673dd41ea021b896d5203f3ba7c。我们可以通过获取 source 的输出或 sink 的输入来统计 flink 的整体吞吐

###### 进程级别CPU信息

- 获取 TM 的metrics：`url = 'http://localhost:8081/taskmanagers/'+tm_id+'/metrics?get=Status.JVM.CPU.Time'`

- 获取 TM 的信息

  - ```j
    http://localhost:8081/taskmanagers/metrics?get=Status.JVM.CPU.Load,Status.JVM.CPU.Time
    ```

  - ```json
    [
      {
        "id": "Status.JVM.CPU.Load",
        "min": 0.001088321473419841,
        "max": 0.001088321473419841,
        "avg": 0.001088321473419841,
        "sum": 0.001088321473419841
      },
      {
        "id": "Status.JVM.CPU.Time",
        "min": 1.257008E13,
        "max": 1.257008E13,
        "avg": 1.257008E13,
        "sum": 1.257008E13
      }
    ]
    ```

###### 机器级别CPU信息

- 先在lib目录下导入依赖

  - ```java
    wget https://repo1.maven.org/maven2/com/github/oshi/oshi-core/3.4.0/oshi-core-3.4.0.jar
    wget https://repo1.maven.org/maven2/net/java/dev/jna/jna-platform/4.2.2/jna-platform-4.2.2.jar
    wget https://repo1.maven.org/maven2/net/java/dev/jna/jna/4.2.2/jna-4.2.2.jar
    ```

- 获取 TM 所在机器的CPU 利用率

  - ```
    'http://localhost:8081/taskmanagers/'+tm_id+'/metrics?get=System.CPU.Usage'
    ```

- 获取 JM 所在机器的CPU 利用率

  - ```
    http://localhost:8081/jobmanager'+'/metrics?get=System.CPU.Usage
    ```

#### reporter机制

- 描述：使用Flink支持的reporter，让各个节点直接使用reporter把metrics发给相应的数据库
- Flink自带几个reporter，包括：JMX、Graphite、InfluxDB、Prometheus等

##### 实现自定义reporter

- 描述：用户可以使用Flink预留的接口实现自己的reporter，如Flink目前不支持的OpenTSDB

## 工业界的metrics机制的实现

### 前端

- Grafana
  - Grafana就是一个大屏可视化方案，有几种常用的图表
  - 它很酷炫，很方便，很受欢迎，生态很好
  - 但是它不能存储数据，也不能用来画DAG之类的
- 定制化
  - 由于前端需求比较定制化，所以很多公司的前端都会有些定制化的内容

### 后端

- OpenTSDB
  - 可扩展性好，完全开源，美团等集群大的公司就喜欢这个
- Prometheus
  - 完全开源，告警机制不错，生态很好
- InfluxDB
  - 性能非常好，支持SQL，但是集群版闭源收费

## 我的metrics机制的实现

我之前是使用Python不断发送报文，获取一些相应的信息。

但是，这几天我在重新设计后端，因为：

- 用报文获取少量信息还行，获取所有metrics的话：
  - 有一定工作量，因为不同metrics的获取方式不同
  - 系统会非常乱，可维护性差，容易出bug
  - 还要设计存储metrics的机制
- 我需要在HiveVIS定型前有一个较好的前端来呈现一些信息，Grafana就是一个不错的选择，用Grafana的话后端不如也采用业界常用方案
- 工业界对于Flink的metrics收集有不少成熟的方案，可以试试看

所以，现在我打算使用reporter机制了，我的工作包括：

- 一开始经过一番调研后，我打算使用 Prometheus+Grafana，但是不知道为什么搞了半天都调不通

- 然后，我换了另一个业界常用的方案，就是InfluxDB+Grafana，大致搞通了