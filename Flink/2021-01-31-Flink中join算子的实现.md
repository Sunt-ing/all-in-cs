### 核心问题

join的实现依赖于缓存整个数据集，而streaming SQL join的对象是无限的数据流，内存压力和计算效率都是问题。

### 离线batch SQL join的实现

传统的离线batch join有三种实现方式：

- Nested-loop join：将两个数据集加载到内存中，内嵌遍历比较。虽然时间复杂度和空间复杂度都是最高的，但是比较灵活应用广泛，所以其变态BNL经常被传统数据库用于Join操作的默认实现。
- Sort-merge join：先将两个数据集排序，然后遍历匹配，就像merge sort那样。它只适用于equi-join，同时由于排序成本很高，通常是作为输入本就有序时的优化方案。
- Hash-join：把一个数据集转换成哈希表，然后遍历另一个数据集进行匹配。由于对空间要求比较大，所以通常作为其中一个表适合放入内存的优化方案。

### 实时streaming SQL join的实现

Sort-merge join无法在流上应用，而Nested-loop join和Hash-join则可以被使用。随着时间增长，过时数据的剔除就非常重要了。

### Flink Streaming SQL 的join

Regular join：所有数据更新全局可见，一般不可持续，只用作有界数据流的join。

Time-windowed join：给join设置一个时间界限，超出范围的数据对于join不可见，可以被清理掉。时间可以是processing time，或者是event time。一个关键问题是怎么把时间属性加入SQL语句中，目前Flink在通过开发相应的SQL方言来实现。

Temporal table join：类似hash-join，但是小表（build table）是基于append-only数据流的带时间版本的视图（temporal table）。如：使用不断变动的汇率表对订单表进行转换。

### Flink中的其他join

Hybrid-Hash-Join：类似hash-join，但是如果小表还是太大就将小表partition了再hash。如果内存足够，就留一些数据一直到内存中。





### Reference

- [Flink SQL 如何实现数据流的 Join？](https://ververica.cn/developers/how-does-flink-sql-realize-data-flow-join/)

