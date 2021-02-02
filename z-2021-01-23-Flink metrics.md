注册自己的指标的方式

- 注册富函数
- 使用 metricGroup = getRuntimeContext().getMetricGroup( ) 来注册新指标

指标类型

- Counter
  - 可增可减
  - 也可以自己定义一个 Counter

- Gauge

  - 可以是任何类型的数据
  - 实现 Gauge 接口，再重写一下 toString( )       方法以用于生成 report

- Histogram

  - 用来监测 long value
  - Codahale/DropWizard histograms

- Meter

  - 监测平均产出

指标名表示方法：

system-provided scope     + optional user-defined scope + user-defined name