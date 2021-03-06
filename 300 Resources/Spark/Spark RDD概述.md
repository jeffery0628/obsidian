---
Create: 2021年 十二月 1日, 星期三 09:32
tags: 
  - Engineering/spark
  - 大数据
---

# 概述
RDD叫做弹性分布式数据集，是Spark中最基本的数据抽象。在代码中是一个抽象类，它代表一个弹性的、不可变、可分区、里面的元素可并行计算的集合。

RDD 表示只读的分区的数据集，对 RDD 进行改动，只能通过 RDD 的转换操作, 然后得到新的 RDD, 并不会对原 RDD 有任何的影响，在 Spark 中, 所有的工作要么是创建 RDD, 要么是转换已经存在 RDD 成为新的 RDD, 要么在 RDD 上去执行一些操作来得到一些计算结果。

每个 RDD 被切分成多个分区(**partition**), 每个分区可能会在集群中不同的节点上进行计算.

## RDD特点

### 弹性

存储的弹性：内存与磁盘的自动切换；
容错的弹性：数据丢失可以自动恢复；
计算的弹性：计算出错重试机制；
分片的弹性：可根据需要重新分片。
### 分区

RDD 逻辑上是分区的，每个分区的数据是抽象存在的，计算的时候会通过一个**compute**函数得到每个分区的数据。

如果 RDD 是通过已有的文件系统构建，则**compute**函数是读取指定文件系统中的数据，如果 RDD 是通过其他 RDD 转换而来，则 **compute**函数是执行转换逻辑将其他 RDD 的数据进行转换。



### 只读

RDD 是只读的，要想改变 RDD 中的数据，只能在现有 RDD 基础上创建新的 RDD。由一个 RDD 转换到另一个 RDD，可以通过丰富的转换算子实现，不再像 MapReduce 那样只能写**map**和**reduce**了。

RDD 的操作算子包括两类：

- 一类叫做**transformation**，它是用来将 RDD 进行转化，构建 RDD 的血缘关系；
-  另一类叫做**action**，它是用来触发 RDD 进行计算，得到 RDD 的相关计算结果或者 保存 RDD 数据到文件系统中.



### 依赖

RDDs 通过操作算子进行转换，转换得到的新 RDD 包含了从其他 RDDs 衍生所必需的信息，RDDs 之间维护着这种血缘关系，也称之为依赖。

依赖包括两种：

- 一种是窄依赖，RDDs 之间分区是一一对应的。
- 另一种是宽依赖，下游 RDD 的每个分区与上游 RDD的每个分区都有关，是多对多的关系。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-intro/image-20210919145522803.png)

### 缓存

如果在应用程序中多次使用同一个 RDD，可以将该 RDD 缓存起来，该 RDD 只有在第一次计算的时候会根据血缘关系得到分区的数据，在后续其他地方用到该 RDD 的时候，会直接从缓存处取而不用再根据血缘关系计算，这样就加速后期的重用。

如下图所示，`RDD-1 `经过一系列的转换后得到 `RDD-n `并保存到 hdfs，`RDD-1` 在这一过程中会有个中间结果，如果将其缓存到内存，那么在随后的 `RDD-1 `转换到 `RDD-m` 这一过程中，就不会计算其之前的 RDD 了。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-intro/image-20210919145632812.png)

### checkpoint

虽然 RDD 的血缘关系天然地可以实现容错，当 RDD 的某个分区数据计算失败或丢失，可以通过血缘关系重建。

但是对于长时间迭代型应用来说，随着迭代的进行，RDDs 之间的血缘关系会越来越长，一旦在后续迭代过程中出错，则需要通过非常长的血缘关系去重建，势必影响性能。

为此，RDD 支持**checkpoint** 将数据保存到持久化的存储中，这样就可以切断之前的血缘关系，因为checkpoint 后的 RDD 不需要知道它的父 RDDs 了，它可以从 checkpoint 处拿到数据。


## 五个重要属性
### 多个分区
```scala
protected def getPartitions: Array[Partition]
```
分区可以看成是数据集的基本组成单位。对于 RDD 来说, 每个分区都会被一个计算任务处理, 并决定了并行计算的粒度。
用户可以在创建 RDD 时指定 RDD 的分区数, 如果没有指定, 那么就会采用默认值，默认值就是程序所分配到的 CPU Core 的数目。
每个分配的存储是由BlockManager 实现的. 每个分区都会被逻辑映射成 BlockManager 的一个 Block, 而这个 Block 会被一个 Task 负责计算。

### 计算每个分区函数
```scala
def compute(split: Partition, context: TaskContext): Iterator[T]
```
  Spark 中 RDD 的计算是以分区（分片）为单位的, 每个 RDD 都会实现 **compute** 函数以达到这个目的。

### 与其他 RDD 之间的依赖关系
```scala
protected def getDependencies: Seq[Dependency[_]] = deps
```
RDD 的每次转换都会生成一个新的 RDD, 所以 RDD 之间会形成类似于流水线一样的前后依赖关系. 在部分分区数据丢失时, Spark 可以通过这个依赖关系重新计算丢失的分区数据, 而不是对 RDD 的所有分区进行重新计算。

### 对存储键值对的 RDD, 还有一个可选的分区器
```scala
val partitioner: Option[Partitioner] = None
```
只有对于 **key-value**的 RDD, 才会有 **Partitioner**, 非**key-value**的 RDD 的 **Partitioner** 的值是 **None**. **Partitiner** 不但决定了 RDD 的分区数量, 也决定了 parent RDD Shuffle 输出时的分区数量.



### 存储每个分区优先位置的列表
```scala
protected def getPreferredLocations(split: Partition): Seq[String] = Nil
```
比如对于一个 HDFS 文件来说, 这个列表保存的就是每个 **Partition** 所在文件块的位置. 按照“移动数据不如移动计算”的理念, Spark 在进行任务调度的时候, 会尽可能地将计算任务分配到其所要处理数据块的存储位置。















