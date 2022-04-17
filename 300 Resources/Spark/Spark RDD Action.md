---
Create: 2021年 十二月 1日, 星期三 09:50
tags: 
  - Engineering/spark
  - 大数据
---




# Action



## reduce
函数签名：
```scala
def reduce(f: (T, T) => T): T
```
通过**func**函数聚集 RDD 中的所有元素，先聚合分区内数据，再聚合分区间数据。

```scala
val rdd1 = sc.parallelize(1 to 100).reduce(_+_)
val rdd1 = sc.parallelize(Array(("a", 1), ("b", 2), ("c", 3))).reduce((item1,item2)=>(item1._1+item2._1,item1._2+item2._2))
```

## collect
函数签名：
```scala
def collect(): Array[T]
```
以==数组==的形式返回 RDD 中的所有元素.所有的数据都会被拉到 driver 端, 所以要慎用
![[700 Attachments/Pasted image 20220314101256.png]]

## count
函数签名：
```scala
def count(): Long
```
返回 RDD 中元素的个数(所有分区中的元素个数总和).

## take
函数签名：
```
def take(num: Int): Array[T]
```

返回 RDD 中前 n 个元素组成的数组，take 的数据也会拉到 driver 端, 应该只对小数据集使用。

```scala
val rdd1 = sc.parallelize(Array(("a", 1), ("b", 2), ("c", 3)))
rdd1.take(2).foreach(println)
```
![[700 Attachments/Pasted image 20220314101647.png]]

## first

函数签名： 
```scala
def first(): T
```
返回 RDD 中的第一个元素. 类似于**take(1)**.

## takeOrder
函数签名：
```scala
def takeOrdered(num: Int)(implicit ord: Ordering[T]): Array[T]
```
返回排序后的前 **n** 个元素, 默认是升序排列.

```scala
val rdd1 = sc.parallelize(Array(100, 20, 130, 500, 60))
rdd1.takeOrdered(3).foreach(println)  // 20  60  100
```
## aggregate
函数签名： 
```scala
def aggregate[U: ClassTag](zeroValue: U)(seqOp: (U, T) => U, combOp: (U, U) => U): U
```

作用：**aggregate**函数将每个分区里面的元素通过**seqOp**和初始值进行聚合，然后用**combine**函数将每个分区的结果和初始值(**zeroValue**)进行**combine**操作。这个函数最终返回的类型不需要和RDD中元素类型一致

> **zeroValue** 分区内聚合和分区间聚合的时候各会使用一次.

```scala
val rdd1 = sc.parallelize(Array(100, 30, 10, 30, 1, 50, 1, 60, 1), 2)
rdd1.aggregate(0)((u,v)=>(u+v),(u1,u2)=>(u1+u2))  // 283

val rdd1 = sc.parallelize(Array("a", "b", "c", "d"), 2)
rdd1.aggregate("x")((u,v)=>(u+v),(u1,u2)=>(u1+u2))  // xxabxcd
```
> 注意：分区间逻辑再次使用初始值和aggregateByKey是有区别的。

## fold
函数签名：
```scala
def fold(zeroValue: T)(op: (T, T) => T): T
```
折叠操作，**aggregate**的简化操作，**seqop**和**combop**一样的时候,可以使用**fold**

```scala
val rdd1 = sc.parallelize(Array("a", "b", "c", "d"), 2)
println(rdd1.fold("x")((u, v) => (u + v)))  // xxabxcd
```

## saveAsTextFile

将数据集的元素以**textfile**的形式保存到**HDFS**文件系统或者其他支持的文件系统，对于每个元素，Spark 将会调用**toString**方法，将它装换为文件中的文本

```scala
val rdd1 = sc.parallelize(Array("a", "b", "c", "d"), 2)
rdd1.saveAsTextFile("src/main/resources/result.txt")
```

> result.txt 文件不可提前存在
两个分区，part-00000保存了分区0的数据，part-00001保存了分区1的数据

## saveAsSequenceFile

作用：将数据集中的元素以 Hadoop sequencefile 的格式保存到指定的目录下，可以使 HDFS 或者其他 Hadoop 支持的文件系统。

## saveAsObjectFile

作用：用于将 RDD 中的元素序列化成对象，存储到文件中。

## countByKey
函数签名：
```scala
def countByKey(): Map[K, Long]
```

作用：针对**(K,V)**类型的 RDD，返回一个**(K,Int)**的**map**，表示每一个**key**对应的元素个数。

```scala
val rdd1 = sc.parallelize(Array(("a", 10), ("a", 20), ("b", 100), ("c", 200)))
rdd1.countByKey().foreach(println) # (c,1) (a,2) (b,1)
```

## foreach
函数签名：
```scala
def foreach(f: T => Unit): Unit
```
作用: 针对 RDD 中的每个元素都执行一次**func**
![[700 Attachments/Pasted image 20220314102436.png]]







