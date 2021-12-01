---
Create: 2021年 十二月 1日, 星期三 10:03
tags: 
  - Engineering/spark
  - 大数据
---

# 查看RDD的血缘关系



```scala
val rdd1 = sc.parallelize(Array("hello world", "hello world","hello jeffery", "jeffery", "hahah"), 2)
val rdd2 = rdd1.map((_,1)).reduceByKey(_+_)
val result_debug = rdd2.toDebugString
println(result_debug)
```

结果：

```
(2) ShuffledRDD[2] at reduceByKey at scala.scala:29 []
 +-(2) MapPartitionsRDD[1] at map at scala.scala:29 []
    |  ParallelCollectionRDD[0] at parallelize at scala.scala:28 []
```

> 圆括号中的数字表示 RDD 的并行度. 也就是有几个分区.



# 查看RDD的依赖关系

```scala
val rdd1 = sc.parallelize(Array("hello world", "hello world","hello jeffery", "jeffery", "hahah"), 2)
val rdd2 = rdd1.map((_,1)).reduceByKey(_+_)

println(rdd1.dependencies)  // List()
println(rdd2.dependencies)  // List(org.apache.spark.ShuffleDependency@2c8662ac)
```

>  RDD 之间的关系可以从两个维度来理解: 
>
> - 一个是 RDD 是从哪些 RDD 转换而来, 也就是 RDD 的 parent RDD(s)是什么; 
> - 另一个就是 RDD 依赖于 parent RDD(s)的哪些 Partition(s). 这种关系就是 RDD 之间的依赖.


依赖 有 2 种策略:

1. 窄依赖(transformations with narrow dependencies)
2. 宽依赖(transformations with wide dependencies)

宽依赖对 Spark 去评估一个 transformations 有更加重要的影响, 比如对性能的影响.

## 窄依赖

如果 `B` RDD 是由 `A` RDD 计算得到的, 则 `B` RDD 就是 Child RDD, `A` RDD 就是 parent RDD.

如果依赖关系在设计的时候就可以确定, 而不需要考虑父 RDD 分区中的记录, 并且如果父 RDD 中的每个分区最多只有一个子分区, 这样的依赖就叫窄依赖。一句话总结: 父 RDD 的每个分区最多被一个 RDD 的分区使用

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920164216424.png)

具体来说, 窄依赖的时候, 子 RDD 中的分区要么只依赖一个父 RDD 中的一个分区(比如**map**, **filter**操作), 要么在设计时候就能确定子 RDD 是父 RDD 的一个子集(比如: **coalesce**)。所以, 窄依赖的转换可以在任何的的一个分区上单独执行, 而不需要其他分区的任何信息。

## 宽依赖

如果 父 RDD 的分区被不止一个子 RDD 的分区依赖, 就是宽依赖。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920164317251.png)

宽依赖工作的时候, 不能随意在某些记录上运行, 而是需要使用特殊的方式(比如按照 key)来获取分区中的所有数据.

例如: 在排序(**sort**)的时候, 数据必须被分区, 同样范围的 **key** 必须在同一个分区内. 具有宽依赖的 **transformations** 包括: **sort**, **reduceByKey**, **groupByKey**, **join**, 和调用**rePartition**函数的任何操作。







