---
Create: 2021年 十二月 1日, 星期三 10:07
tags: 
  - Engineering/spark
  - 大数据
---
# 数据分区器

对于只存储 **value**的 RDD, 不需要分区器，只有存储**Key-Value**类型的才会需要分区器。

Spark 目前支持 ==Hash== 分区和 ==Range== 分区，用户也可以自定义分区，Hash 分区为当前的默认分区，Spark 中分区器直接决定了 RDD 中分区的个数、RDD 中每条数据经过 Shuffle 过程后属于哪个分区和 Reduce 的个数。

> 注意：
> 1. 只有Key-Value类型的RDD才有分区器，非Key-Value类型的RDD分区的值是None
> 2. 每个RDD的分区ID范围：0~numPartitions-1，决定这个值是属于那个分区的。

## 查看RDD的分区

### value 的分区器

```scala
val rdd1 = sc.parallelize(Array(1,2,3,4,5))
println(rdd1.partitioner)  // None
```

### key-value 的分区器

```scala
val rdd1 = sc.parallelize(Array(("hello", 1), ("world", 1)))
println(rdd1.partitioner) // None
```

```scala
import org.apache.spark.HashPartitioner
val rdd1 = sc.parallelize(Array(("hello", 1), ("world", 1)))
val rdd2 = rdd1.partitionBy(new HashPartitioner(3))
println(rdd2.partitioner)  // Some(org.apache.spark.HashPartitioner@3)
```

## HashPartitioner

**HashPartitioner**分区的原理：对于给定的**key**，计算其**hashCode**，并除以分区的个数取余，如果余数小于 0，则用**余数+分区的个数**（否则加0），最后返回的值就是这个**key**所属的分区**ID**。

```scala 源码
/* Calculates 'x' modulo 'mod', takes to consideration sign of x,
  * i.e. if 'x' is negative, than 'x' % 'mod' is negative too
  * so function return (x % mod) + mod in that case.
  */
def nonNegativeMod(x: Int, mod: Int): Int = {
    val rawMod = x % mod
    rawMod + (if (rawMod < 0) mod else 0)
}
```

```scala
import org.apache.spark.HashPartitioner
val rdd1 = sc.parallelize(Array((10, "a"), (20, "b"), (31, "c"), (40, "d"), (50, "e"), (60, "f")))
// 把分区号取出来, 检查元素的分区情况
val rdd2: RDD[(Int, String)] = rdd1.mapPartitionsWithIndex((index, it) => it.map(x => (index, x._1 + " : " + x._2)))
println(rdd2.collect.mkString(","))

// 把 RDD1使用 HashPartitioner重新分区
val rdd3 = rdd1.partitionBy(new HashPartitioner(5))
// 检测RDD3的分区情况
val rdd4: RDD[(Int, String)] = rdd3.mapPartitionsWithIndex((index, it) => it.map(x => (index, x._1 + " : " + x._2)))
println(rdd4.collect.mkString(","))
```

结果：

```
(0,10 : a),(0,20 : b),(0,30 : c),(1,40 : d),(1,50 : e),(1,60 : f)  默认分区
(0,10 : a),(0,20 : b),(0,40 : d),(0,50 : e),(0,60 : f),(1,31 : c)  使用hash分区，注意31
```

> HashPartitioner分区弊端：可能导致每个分区中数据量的不均匀，极端情况下会导致某些分区拥有RDD的全部数据。

## RangePartitioner

**HashPartitioner** 分区弊端： 可能导致每个分区中数据量的不均匀，极端情况下会导致某些分区拥有 RDD 的全部数据。比如前面的例子就是一个极端, 只有31进入分区了。



**RangePartitioner** 作用：将一定范围内的数映射到某一个分区内，尽量保证每个分区中数据量的均匀，而且分区与分区之间是有序的，一个分区中的元素肯定都是比另一个分区内的元素小或者大，但是分区内的元素是不能保证顺序的。简单的说就是将一定范围内的数映射到某一个分区内。实现过程为：

- 第一步：先从整个 RDD 中抽取出样本数据，将样本数据排序，计算出每个分区的最大 **key** 值，形成一个**Array[KEY]**类型的数组变量 **rangeBounds**；(边界数组)。
- 第二步：判断**key**在**rangeBounds**中所处的范围，给出该**key**值在下一个**RDD**中的分区**id**下标；该分区器要求 RDD 中的 **KEY** 类型必须是可以排序的。比如[1,100,200,300,400]，然后对比传进来的**key**，返回对应的分区**id**。

## 自定义分区器

要实现自定义的分区器，需要继承 **org.apache.spark.Partitioner**, 并且需要实现下面的方法:

1. `numPartitions`:该方法需要返回分区数, 必须要大于0.
2. `getPartition(key)`:返回指定键的分区编号(0到numPartitions-1)。
3. `equals`:Java 判断相等性的标准方法。这个方法的实现非常重要，Spark 需要用这个方法来检查分区器对象是否和其他分区器实例相同，这样 Spark 才可以判断两个 RDD 的分区方式是否相同
4. `hashCode`:  如果覆写了**equals**, 则也应该覆写这个方法.

```scala
import org.apache.spark.Partitioner

class MyPartitioner(numPars: Int) extends Partitioner {
  override def numPartitions: Int = numPars
  override def getPartition(key: Any): Int = {
    1
  }
}


val rdd1 = sc.parallelize(Array((10, "a"), (20, "b"), (30, "c"), (40, "d"), (50, "e"), (60, "f")), 3)
val rdd2: RDD[(Int, String)] = rdd1.partitionBy(new MyPartitioner(4))
val rdd3: RDD[(Int, String)] = rdd2.mapPartitionsWithIndex((index, items) => items.map(x => (index, x._1 + " : " + x._2)))
println(rdd3.collect.mkString(" "))


```







