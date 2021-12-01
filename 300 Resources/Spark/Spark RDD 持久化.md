---
Create: 2021年 十二月 1日, 星期三 10:06
tags: 
  - Engineering/spark
  - 大数据
---
# RDD 持久化

每碰到一个 Action 就会产生一个 job, 每个 job 开始计算的时候总是从这个 job 最开始的 RDD 开始计算。

```scala
val rdd1 = sc.parallelize(Array("ab", "bc"))
val rdd2 = rdd1.flatMap(x => {
    println("flatMap...")
    x.split("")
	})
val rdd3: RDD[(String, Int)] = rdd2.map(x => {
    (x, 1)
	})
rdd3.collect.foreach(println)
println("-----------")
rdd3.collect.foreach(println)
```

结果：

```
flatMap...
flatMap...
(a,1)
(b,1)
(b,1)
(c,1)
-----------
flatMap...
flatMap...
(a,1)
(b,1)
(b,1)
(c,1)
```

> 1. 每调用一次 **collect**, 都会创建一个新的 job, 每个 job 总是从它血缘的起始开始计算. 所以, 会发现中间的这些计算过程都会重复的执行.
> 2. 原因是 **rdd**记录了整个计算过程. 如果计算的过程中出现哪个分区的数据损坏或丢失, 则可以从头开始计算来达到容错的目的.



## RDD数据持久化

每个 job 都会重新进行计算, 在有些情况下是没有必要, 如何解决这个问题呢?

Spark 一个重要能力就是可以持久化数据集在内存中。当我们持久化一个 RDD 时, 每个节点都会存储他在内存中计算的那些分区, 然后在其他的 action 中可以重用这些数据. 这个特性会让将来的 action 计算起来更快(通常块 10 倍)。 对于迭代算法和快速交互式查询来说, 缓存(Caching)是一个关键工具。

可以使用方法**persist()**或者**cache()**来持久化一个 RDD，在第一个 action 会计算这个 RDD, 然后把结果的存储到他的节点的内存中。Spark 的 Cache 也是容错的: 如果 RDD 的任何一个分区的数据丢失了, Spark 会自动的重新计算。

RDD 的各个 Partition 是相对独立的, 因此只需要计算丢失的部分即可, 并不需要重算全部 Partition。

另外, spark允许我们对持久化的 RDD 使用不同的存储级别。例如: 可以存在磁盘上, 存储在内存中(堆内存中), 跨节点做复本.可以给**persist()**来传递存储级别. **cache()**方法是使用默认存储级别。



| 存储级别                               | 含义                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| MEMORY_ONLY                            | 将RDD存储为JVM中反序列化的Java对象。如果RDD不适合存储在内存，一些分区将不会被缓存，并且会在每次需要时被重新计算。这是默认级别。 |
| MEMORY_AND_DISK                        | 将RDD存储为JVM中反序列化的Java对象。如果RDD不适合存储在内存，将不适合的分区存储在磁盘上，并在需要时从那里读取它们。 |
| MEMORY_ONLY_SER (Java and Scala)       | 将RDD存储为**序列化**的Java对象(每个分区一个字节数组)。这通常比反序列化的对象更节省空间，尤其是在使用快速序列化程序时，但读取起来更耗费CPU。 |
| MEMORY_AND_DISK_SER (Java and Scala)   | 与 MEMORY_ONLY_SER类似, 但是将不适合内存的分区溢出到磁盘上，而不是在每次需要时动态地重新计算它们。 |
| DISK_ONLY                              | 将RDD仅缓存到磁盘                                            |
| MEMORY_ONLY_2, MEMORY_AND_DISK_2, etc. | 与上面的级别相同，但在两个群集节点上复制每个分区。           |
| OFF_HEAP (experimental)                | 类似于MEMORY_ONLY_SER，但是将数据存储在堆外内存中。这需要启用堆外内存 |

```scala
val rdd1 = sc.parallelize(Array("ab", "bc"))
val rdd2 = rdd1.flatMap(x => {
println("flatMap...")
	x.split("")
	})
rdd2.persist(StorageLevel.MEMORY_ONLY)  // rdd 持久化

val rdd3: RDD[(String, Int)] = rdd2.map(x => {
	(x, 1)
	})
rdd3.collect.foreach(println)
println("-----------")
rdd3.collect.foreach(println)
```

结果：

```
flatMap...
flatMap...
(a,1)
(b,1)
(b,1)
(c,1)
-----------
(a,1)
(b,1)
(b,1)
(c,1)
```

> 1. 第一个 job 会计算 RDD2, 以后的 job 就不用再计算了.
> 2. 有一点需要说明的是, 即使不手动设置持久化, Spark 也会自动的对一些 shuffle 操作的中间数据做持久化操作(比如: reduceByKey). 这样做的目的是为了当一个节点 shuffle 失败了避免重新计算整个输入. 当时, 在实际使用的时候, 如果想重用数据, 仍然建议调用**persist** 或 **cache**



## checkpoint

Spark 中对于数据的保存除了持久化操作之外，还提供了一种检查点的机制,检查点（本质是通过将RDD写入Disk做检查点）是为了通过 Lineage 做容错的辅助。

Lineage 过长会造成容错成本过高，这样就不如在中间阶段做检查点容错，如果之后有节点出现问题而丢失分区，从做检查点的 RDD 开始重做 Lineage，就会减少开销。

检查点通过将数据写入到 HDFS 文件系统实现了 RDD 的检查点功能。

为当前 RDD 设置检查点。该函数将会创建一个二进制的文件，并存储到 checkpoint 目录中，该目录是用 **SparkContext.setCheckpointDir()**设置的。在 checkpoint 的过程中，该RDD 的所有依赖于父 RDD中 的信息将全部被移除。

对 RDD 进行 checkpoint 操作并不会马上被执行，必须执行 Action 操作才能触发, 在触发的时候需要对这个 RDD 重新计算。

```scala
def main(args: Array[String]): Unit = {

  // 要在SparkContext初始化之前设置, 否则无效
  System.setProperty("HADOOP_USER_NAME", "checkpoint")
  val spark = SparkSession.builder()
    .config("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
    // use this if you need to increment Kryo buffer size. Default 64k
    .config("spark.kryoserializer.buffer", "1024k")
    // use this if you need to increment Kryo buffer max size. Default 64m
    .config("spark.kryoserializer.buffer.max", "1024m")
    /*
    * Use this if you need to register all Kryo required classes.
    * If it is false, you do not need register any class for Kryo, but it will increase your data size when the data is serializing.
    */
    .config("spark.kryo.registrationRequired", "true")
    .master("local[2]")
    .enableHiveSupport()
    .getOrCreate()

  val sc = spark.sparkContext

  // 设置 checkpoint的目录. 如果spark运行在集群上, 则必须是 hdfs 目录
  sc.setCheckpointDir("src/main/resources/checkpoint")
  val rdd1 = sc.parallelize(Array("abc"))
  val rdd2: RDD[String] = rdd1.map(_ + " : " + System.currentTimeMillis())

  /*
  标记 RDD2的 checkpoint.
  RDD2会被保存到文件中(文件位于前面设置的目录中), 并且会切断到父RDD的引用, 也就是切断了它向上的血缘关系
  该函数必须在job被执行之前调用.
  强烈建议把这个RDD序列化到内存中, 否则, 把他保存到文件的时候需要重新计算.
   */
  rdd2.checkpoint()
  // rdd2.cache()
  rdd2.collect().foreach(println)
  rdd2.collect().foreach(println)
  rdd2.collect().foreach(println)
  sc.stop()
}
```

结果：

```
abc : 1632130182619
abc : 1632130182743
abc : 1632130182743

若果在chechpoint之后加上cache：
abc : 1632130326619
abc : 1632130326619
abc : 1632130326619
```

## 持久化和checkpoint的区别

持久化只是将数据保存在 BlockManager 中，而 RDD 的 Lineage 是不变的。但是**checkpoint** 执行完后，RDD 已经没有之前所谓的依赖 RDD 了，而只有一个强行为其设置的**checkpointRDD**，RDD 的 Lineage 改变了。

持久化的数据丢失可能性更大，磁盘、内存都可能会存在数据丢失的情况。但是 **checkpoint** 的数据通常是存储在如 HDFS 等容错、高可用的文件系统，数据丢失可能性较小。

> 注意: 默认情况下，如果某个 RDD 没有持久化，但是设置了**checkpoint**，会存在问题. 本来这个 job 都执行结束了，但是由于中间 RDD 没有持久化，checkpoint job 想要将 RDD 的数据写入外部文件系统的话，需要全部重新计算一次，再将计算出来的 RDD 数据 checkpoint到外部文件系统。 所以，建议对 **checkpoint()**的 RDD 使用持久化, 这样 RDD 只需要计算一次就可以了.









