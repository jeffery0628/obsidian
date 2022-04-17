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

## RDD Cache 缓存
RDD通过Cache或者Persist方法将前面的计算结果缓存，默认情况下会把数据以序列化的形式缓存在JVM的堆内存中。但是并不是这两个方法被调用时立即缓存，而是触发后面的action时，该RDD将会被缓存在计算节点的内存中，并供后面重用。

![[700 Attachments/Pasted image 20220314222200.png]]
```scala
  def main(args: Array[String]): Unit = {
    //1.创建SparkConf并设置App名称
    val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
    //2.创建SparkContext，该对象是提交Spark App的入口
    val sc: SparkContext = new SparkContext(conf)
    //3. 创建一个RDD，读取指定位置文件:hello atguigu atguigu
    val lineRdd: RDD[String] = sc.makeRDD(List("hello","spark","hello scala","hello hadoop"))
    //3.1.业务逻辑
    val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))
    val wordToOneRdd: RDD[(String, Int)] = wordRdd.map((_,1))
    //3.5 cache操作会增加血缘关系，不改变原有的血缘关系
    println(wordToOneRdd.toDebugString)
    //3.4 数据缓存。
    wordToOneRdd.cache()
    //3.6 可以更改存储级别
    // wordToOneRdd.persist(StorageLevel.MEMORY_AND_DISK_2)
    //3.2 触发执行逻辑
    wordToOneRdd.collect()
    println("-----------------")
    println(wordToOneRdd.toDebugString)
    //3.3 再次触发执行逻辑
    wordToOneRdd.collect()
    //4.关闭连接
    sc.stop()
  }
```
> 注意：默认的存储级别都是仅在内存存储一份。

![[700 Attachments/Pasted image 20220314222617.png]]
缓存有可能丢失，或者存储于内存的数据由于内存不足而被删除，RDD的缓存容错机制保证了即使缓存丢失也能保证计算的正确执行。通过基于RDD的一系列转换，丢失的数据会被重算，由于RDD的各个Partition是相对独立的，因此只需要计算丢失的部分即可，并不需要重算全部Partition。

### 自带缓存算子
Spark会自动对一些Shuffle操作的中间数据做持久化操作(比如：reduceByKey)。这样做的目的是为了当一个节点Shuffle失败了避免重新计算整个输入。但是，在实际使用的时候，如果想重用数据，仍然建议调用persist或cache。
```scala
def main(args: Array[String]): Unit = {  
  //1.创建SparkConf并设置App名称  
 val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")  
  
  //2.创建SparkContext，该对象是提交Spark App的入口  
 val sc: SparkContext = new SparkContext(conf)  
  
  //3. 创建一个RDD，读取指定位置文件:hello atguigu atguigu  
 val lineRdd: RDD[String] = sc.makeRDD(List("hello","spark","hello scala","hello hadoop"))  
  
  //3.1.业务逻辑  
 val wordRdd: RDD[String] = lineRdd.flatMap(line => line.split(" "))  
  
  val wordToOneRdd: RDD[(String, Int)] = wordRdd.map((_,1))  
  
  // 采用reduceByKey，自带缓存  
 val wordByKeyRDD: RDD[(String, Int)] = wordToOneRdd.reduceByKey(_+_)  
  
  //3.5 cache操作会增加血缘关系，不改变原有的血缘关系  
 println(wordByKeyRDD.toDebugString)  
  
  //3.4 数据缓存。  
 //wordByKeyRDD.cache()  
 //3.2 触发执行逻辑 wordByKeyRDD.collect()  
  
  println("-----------------")  
  println(wordByKeyRDD.toDebugString)  
  
  //3.3 再次触发执行逻辑  
 wordByKeyRDD.collect()  
  
  //4.关闭连接  
 sc.stop()  
}

```
访问http://localhost:4040/jobs/页面，查看第一个和第二个job的DAG图。说明：增加缓存后血缘依赖关系仍然有，但是，第二个job取的数据是从缓存中取的。
![[700 Attachments/Pasted image 20220314223039.png]]



## RDD persist 缓存

行动算子会触发每个 job 都会重新进行计算, 在有些情况下是没有必要, 如何解决这个问题呢?

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

1. ==为什么要做检查点==？
	Lineage 过长会造成容错成本过高，这样就不如在中间阶段做检查点容错，如果之后有节点出现问题而丢失分区，从做检查点的 RDD 开始重做 Lineage，就会减少开销。

2. ==检查点存储路径==：
	检查点通过将数据写入到 HDFS 文件系统实现了 RDD 的检查点功能。
 

3. ==检查点数据存储格式为==：
	为当前 RDD 设置检查点。该函数将会创建一个二进制的文件，并存储到 checkpoint 目录中，该目录是用 **SparkContext.setCheckpointDir()**设置的。

4. ==检查点切断血缘==：
	在 checkpoint 的过程中，该RDD 的所有依赖于父 RDD中 的信息将全部被移除。

5. ==检查点触发时间==：
	对 RDD 进行 checkpoint 操作并不会马上被执行，必须执行 Action 操作才能触发, 在触发的时候需要对这个 RDD 重新计算。![[700 Attachments/Pasted image 20220314224009.png]]

 

6. 设置检查点步骤
	1. 设置检查点数据存储路径：sc.setCheckpointDir("./checkpoint1")
	2. 调用检查点方法：wordToOneRdd.checkpoint()

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

 

访问http://localhost:4040/jobs/页面，查看4个job的DAG图。其中第2个图是checkpoint的job运行DAG图。第3、4张图说明，检查点切断了血缘依赖关系。
![[700 Attachments/Pasted image 20220314224305.png]]

 
1. 只增加checkpoint，没有增加Cache缓存打印：
	第1个job执行完，触发了checkpoint，第2个job运行checkpoint，并把数据存储在检查点上。第3、4个job，数据从检查点上直接读取。

2. 增加checkpoint，也增加Cache缓存打印
	第1个job执行完，数据就保存到Cache里面了，第2个job运行checkpoint，直接读取Cache里面的数据，并把数据存储在检查点上。第3、4个job，数据从检查点上直接读取。

## 持久化和checkpoint的区别
1. Cache缓存只是将数据保存起来，不切断血缘依赖。Checkpoint检查点切断血缘依赖。
2. Cache缓存的数据通常存储在磁盘、内存等地方，可靠性低。Checkpoint的数据通常存储在HDFS等容错、高可用的文件系统，可靠性高。
3. 建议对checkpoint()的RDD使用Cache缓存，这样checkpoint的job只需从Cache缓存中读取数据即可，否则需要再从头计算一次RDD。
4. 如果使用完了缓存，可以通过unpersist（）方法释放缓存


> 如果检查点数据存储到HDFS集群，要注意配置访问集群的用户名。否则会报访问权限异常。


> 注意: 默认情况下，如果某个 RDD 没有持久化，但是设置了**checkpoint**，会存在问题. 本来这个 job 都执行结束了，但是由于中间 RDD 没有持久化，checkpoint job 想要将 RDD 的数据写入外部文件系统的话，需要全部重新计算一次，再将计算出来的 RDD 数据 checkpoint到外部文件系统。 所以，建议对 **checkpoint()**的 RDD 使用持久化, 这样 RDD 只需要计算一次就可以了.










