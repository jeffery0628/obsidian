---
Create: 2021年 十二月 1日, 星期三 09:36
tags: 
  - Engineering/spark
  - 大数据
---
# 概述

在 Spark 中，RDD 被表示为对象，通过对象上的方法调用来对 RDD 进行转换。

经过一系列的转换定义 RDD 之后，就可以调用 `action`触发 RDD 的计算。`action`可以是向应用程序返回结果，或者是向存储系统保存数据。

在Spark中，只有遇到`action`，才会执行 RDD 的计算(即延迟计算)，这样在运行时可以通过管道的方式传输多个转换。

要使用 Spark，开发者需要编写一个 Driver 程序，它被提交到集群以调度运行 Worker

Driver 中定义了一个或多个 RDD，并调用 RDD 上的 action，Worker 则执行 RDD 分区计算任务。
# RDD 的创建

在 Spark 中创建 RDD 的方式可以分为 3 种：

- 从集合中创建RDD
- 从外部存储创建RDD
- 从其他RDD转换得到新的RDD

## 从集合中创建RDD

使用parallelize函数创建

```scala
def main(args: Array[String]): Unit = {

  val sc = SparkSession.builder()
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()
    .sparkContext

  val arr:Array[Int] = Array(10,20,30,40,50,60)
  val rdd1 = sc.parallelize(arr)
  sc.stop()
}
```

使用makeRDD函数创建

```scala
def main(args: Array[String]): Unit = {

  val sc = SparkSession.builder()
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()
    .sparkContext

  val arr:Array[Int] = Array(10,20,30,40,50,60)
  val rdd1 = sc.makeRDD(arr)
  sc.stop()
}
```

>  一旦 RDD 创建成功, 就可以通过并行的方式去操作这个分布式的数据集了.
>
> **parallelize**和**makeRDD**还有一个重要的参数就是把数据集切分成的分区数.
>
> Spark 会为每个分区运行一个任务(task)，正常情况下, Spark 会自动的根据集群来设置分区数

## 从外部存储创建RDD

Spark 也可以从任意 Hadoop 支持的存储数据源来创建分布式数据集，可以是本地文件系统, HDFS, Cassandra, HVase, Amazon S3 等等。Spark 支持文本文件, SequenceFiles, 和其他所有的 Hadoop InputFormat。

```scala
def main(args: Array[String]): Unit = {

  val sc = SparkSession.builder()
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()
    .sparkContext

  val distFile = sc.textFile("src/main/resources/words.txt")
  distFile.foreach(println)
  sc.stop()
}
```

> 1. **url**可以是本地文件系统文件, **hdfs://...**, **s3n://...**等等。如果是使用的本地文件系统的路径, 则必须每个节点都要存在这个路径
>
> 2. 所有基于文件的方法, 都支持目录, 压缩文件, 和通配符(*****). 例如: 
>
> 	```
> 	textFile("/my/directory")
> 	textFile("/my/directory/*.txt")
> 	textFile("/my/directory/*.gz").
> 	```
>
> 3. textFile还可以有第二个参数, 表示分区数。默认情况下, 每个块对应一个分区。可以传递一个大于块数的分区数, 但是不能传递一个比块数小的分区数。



## 从其他RDD转换得到新的RDD

```scala
val distFile = sc.textFile("src/main/resources/words.txt")
val rdd1 = distFile.map((_,1))
```





