---
Create: 2021年 十二月 1日, 星期三 10:05
tags: 
  - Engineering/spark
  - 大数据 
---
# Spark Job 的划分

由于 Spark 的懒执行, 在驱动程序调用一个**action**之前, Spark 应用不会做任何事情。针对每个 `action`, Spark 调度器就创建一个执行图(execution graph)和启动一个 `Spark job`。

每个 job 由多个**stages** 组成, 这些 **stages** 就是实现最终的 RDD 所需的数据转换的步骤。一个宽依赖划分一个 stage。

每个 **stage** 由多个 **tasks** 来组成, 这些 **tasks** 就表示每个并行计算, 并且会在多个执行器上执行.

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920164541516.png)



## DAG 有向无环图

Spark 的顶层调度层使用 RDD 的依赖为每个 job 创建一个由 stages 组成的 DAG(有向无环图). 在 Spark API 中, 这被称作 DAG 调度器(DAG Scheduler)。

注意到, 有些错误, 比如: 连接集群的错误, 配置参数错误, 启动一个 Spark job 的错误, 这些错误必须处理, 并且都表现为 DAG Scheduler 错误. 这是因为一个 Spark job 的执行是被 DAG 来处理。DAG 为每个 job 构建一个 stages 组成的图表, 从而确定运行每个 task 的位置, 然后传递这些信息给 TaskSheduler. TaskSheduler 负责在集群中运行任务。



## Jobs

Spark job 处于 Spark 执行层级结构中的最高层. 每个 Spark job 对应一个 action, 每个 action 被 Spark 应用中的驱动所程序调用。可以把 Action 理解成把数据从 RDD 的数据带到其他存储系统的组件(通常是带到驱动程序所在的位置或者写到稳定的存储系统中)。只要一个 action 被调用, Spark 就不会再向这个 job 增加新的东西。

## Stags

 RDD 的转换是懒执行的, 直到调用一个 action 才开始执行 RDD 的转换。正如前面所提到的, 一个 job 是由调用一个 action 来定义的。一个 action 可能会包含一个或多个转换( transformation ), Spark 根据宽依赖把 job 分解成 stage。

从整体来看, 一个 stage 可以任务是“计算(task)”的集合, 这些每个“计算”在各自的 Executor 中进行运算, 而不需要同其他的执行器或者驱动进行网络通讯. 换句话说, 当任何两个 workers 之间开始需要网络通讯的时候, 这时候一个新的 stage 就产生了, 例如: shuffle 的时候。

这些创建 stage 边界的依赖称为 **ShuffleDependencies**. shuffle 是由宽依赖所引起的, 比如: **sort**, **groupBy**, 因为他们需要在分区中重新分发数据. 那些窄依赖的转换会被分到同一个 stage 中。

```scala
val rdd1 = sc.textFile("src/main/resources/words.txt")
                .flatMap(_.split("  "))
                .map((_,1))
				.reduceByKey(_+_)
                .saveAsTextFile("src/main/resources/word_count_result")
```

> Spark 会把 **flatMap**, **map** 合并到一个 stage 中, 因为这些转换不需要 shuffle。 所以, 数据只需要传递一次, 每个执行器就可以顺序的执行这些操作。
>
> 因为边界 stage 需要同驱动进行通讯, 所以与 job 有关的 stage 通常必须顺序执行而不能并行执行.
>
> 如果这个 stage 是用来计算不同的 RDDs, 被用来合并成一个下游的转换(比如: **join**), 也是有可能并行执行的. 但是仅需要计算一个 RDD 的宽依赖转换必须顺序计算。所以, 设计程序的时候, 尽量少用 **shuffle**.

## Tasks

stage 由 tasks 组成，在执行层级中，task 是最小的执行单位，每一个 task 表现为一个本地计算。

一个 stage 中的所有 tasks 会对不同的数据执行相同的代码(程序代码一样, 只是作用在了不同的数据上)。

一个 task 不能被多个执行器来执行, 但是, 每个执行器会动态的分配多个 slots 来执行 tasks, 并且在整个生命周期内会并行的运行多个 task. 每个 stage 的 task 的数量对应着分区的数量, 即每个 Partition 都被分配一个 Task 

```scala
val rdd1 = sc.parallelize(500 to 50000)
      // stage1
      .filter(_ < 1000)
      .map(x => (x, x))
      //stage2
      .groupByKey()
      .map {case (value, groups) => (groups.sum, value)}
      // stage3
      .sortByKey()
      .count()
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920170526021.png)

> 在大多数情况下, 每个 stage 的所有 task 在下一个 stage 开启之前必须全部完成。









