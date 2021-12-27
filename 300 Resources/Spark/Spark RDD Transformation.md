---
Create: 2021年 十二月 1日, 星期三 09:39
tags: 
  - Engineering/spark
  - 大数据
---



# Value 类型

## `map(func)`

作用: 返回一个新的 RDD, 该 RDD 是由原 RDD 的每个元素经过函数转换后的值而组成. 就是对 RDD 中的数据做转换。

```scala
val rdd1 = sc.parallelize(1 to 10)
val rdd2 = rdd1.map(_*2)
```

## `mapPartitions(func)`

作用：类似于`map(func)`，但是是独立在每个分区上运行，所以func的类型是 `Iterator<T> => Iterator<U>`

假设有`N`个元素，有`M`个分区，那么`map`的函数将被调用`N`次，而`mapPatitions`被调用`M`次，一个函数一次处理所有分区。

```scala
val rdd1 = sc.parallelize(1 to 10)
val rdd2 = rdd1.mapPartitions(it => it.map(_*2))
```


> `map()` 和 `mapPartitions()`的区别
>
> 1. `map()`:每次处理一条数据。
> 2. `mapPartitions()`:每次处理一个分区的数据，这个分区的数据处理完后，原RDD中该分区的数据才能释放，可能导致OOM。
> 3. 当内存空间较大的时候建议使用`mapPartitions()`，以提高处理效率。



## `mapPartitionsWithIndex(func)`

作用：和`mapPartitions(func)`类似，但是会给func多提供一个`Int`值来表示分区的索引。所以func的类型是`((Int,Iterator<T>)=>Iterator<U>)`

```scala
val sc = SparkSession.builder()
  .master("local[3]")
  .enableHiveSupport()
  .getOrCreate()
  .sparkContext

val rdd1 = sc.parallelize(1 to 10)
val rdd2 = rdd1.mapPartitionsWithIndex((index,items)=>items.map(_*2).map((index,_)))
```

结果：

```
(1,8)
(1,10)
(0,2)
(2,14)
(2,16)
(2,18)
(2,20)
(0,4)
(0,6)
(1,12)
```

> 分了三个分区，分区0：1-3，分区1：4-6，分区3：7-10。



对元素进行分区：

```scala
def positions(length:Long,numSlices:Int):Iterator[(Int,Int)] ={
  (0 to numSlices).iterator.map{ i =>

    val start = ((i * length) / numSlices).toInt
    val end = (((i + 1) * length) / numSlices).toInt
    (start,end)

  }
}

seq match{
  case r: Range =>

  case nr: NumericRange[_] =>

  case _ =>
    val array = seq.toArray // To prevent O(n^2) operations for List etc
    positions(array.length, numSlices).map { case (start, end) =>
      array.slice(start, end).toSeq
    }.toSeq
}
```



## `flatMap(func)`

作用：类似于`map`，但是每一个输入元素可以被映射为0或多个输出元素（所以func应该返回一个序列，而不是单一元素），所以func的类型是：`T => TraversableOnce[U]`

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210919203526668.png)



创建一个元素为 `1-5` 的RDD，运用 `flatMap`创建一个新的 RDD，新的 RDD 为原 RDD 每个元素的 平方 和 三次方 来组成:

```scala
val rdd1 = sc.parallelize(1 to 5)
val rdd2 = rdd1.flatMap(x => Array(x*x,x*x*x))
```

## `glom()`

作用: 将每一个分区的元素合并成一个数组，形成新的 RDD 类型是`RDD[Array[T]]`

创建一个4个分区的RDD，并将每个分区的数据放到一个数组

```scala
val rdd1 = sc.parallelize(Array(10,20,30,40,50,60),4)
val rdd2 = rdd1.glom
rdd2.collect.foreach(println)
rdd2.collect.foreach(_.foreach(println))
```

结果：

```scala
[I@6f89292e 
[I@20749d9
[I@de77232
[I@62628e78
rdd2.collect的实际结果是：Array(Array(10), Array(20, 30), Array(40), Array(50, 60))
 
10
20
30
40
50
60
```

## `groupBy(func)`

作用：按照`func`的返回值进行分组。`func`返回值作为 `key`, 对应的值放入一个迭代器中。 返回的RDD类型是： `RDD[(K, Iterable[T])`。(即 (item,group))



每组内元素的顺序不能保证, 并且甚至每次调用得到的顺序也有可能不同。

```scala
val rdd1 = sc.parallelize(Array(1,3,4,20,4,5,8,7,7))
val rdd2 = rdd1.groupBy(x => if(x%2==1) (x,"odd") else (x,"even"))
rdd2.collect.foreach(println)
```

结果：

```
((8,even),CompactBuffer(8))
((1,odd),CompactBuffer(1))
((7,odd),CompactBuffer(7, 7))
((5,odd),CompactBuffer(5))
((3,odd),CompactBuffer(3))
((4,even),CompactBuffer(4, 4))
((20,even),CompactBuffer(20))
```

## `filter(func)`

作用：过滤，返回新的RDD，是由func的返回值为true的元素组成。



创建一个RDD(由字符串组成)，过滤出一个新的 RDD （包含"xiao"的子串）

```scala
val rdd1 = sc.parallelize(Array("xiaoli","laoli","laowang","xiaowang","xiaojing","xiaokong"))
val rdd2 = rdd1.filter(_.contains("xiao"))
rdd2.collect.foreach(println)
```

## `sample(withReplacement,fraction,seed)`

作用:以指定的随机种子随机抽样出比例为**fraction**的数据，(抽取到的数量是: size \* fraction). 需要注意的是得到的结果并不能保证准确的比例.

**withReplacement**表示是抽出的数据是否放回，`true`为有放回的抽样，`false`为无放回的抽样。 放回表示数据有可能会被重复抽取到, `false` 则不可能重复抽取到。如果是`false`, 则`fraction`必须是:**[0,1]**, 是 **true** 则大于等于0就可以了.

`seed`用于指定随机数生成器种子。 一般用默认的, 或者传入当前的`时间戳`。



不放回抽样

```scala
val rdd1 = sc.parallelize(1 to 10)
val rdd2 = rdd1.sample(false,0.5)
```



放回抽样

```scala
val rdd1 = sc.parallelize(1 to 10)
val rdd2 = rdd1.sample(true,0.5)
```

> 抽出的结果存在重复，因此结果的个数可能大于10，但能保证去重之后的元素是原来的一般





## `distinct([numTask])`

作用:对 RDD 中元素执行去重操作，参数表示任务的数量，默认值和分区数保持一致。

```scala
val rdd1 = sc.parallelize(Array(10,10,2,5,3,5,3,6,9,1))
val rdd2 = rdd1.distinct
```



## `coalesce(numPartitions)`

作用: 缩减分区数到指定的数量，用于大数据集过滤后，提高小数据集的执行效率。

```scala
val rdd1 = sc.parallelize(1 to 100, 5)
println(rdd1.partitions.length) # 5
val rdd2 = rdd1.coalesce(2)
println(rdd2.partitions.length) # 2
```

> 注：
>
> 第二个参数表示是否**shuffle**, 如果不传或者传入的为**false**, 则表示不进行**shuffer**, 此时分区数减少有效, 增加分区数无效.

## `repartition(numPartitions)`

作用: 根据新的分区数, 重新 shuffle 所有的数据，这个操作总会通过网络，新的分区数相比以前可以多，也可以少。

```scala
val rdd1 = sc.parallelize(1 to 100, 5)
val rdd2 = rdd1.repartition(3)
println(rdd2.partitions.length)
val rdd3 = rdd1.repartition(10)
println(rdd3.partitions.length)
```

> `coalase`  和 `repartition` 的区别
>
> **coalesce**重新分区，可以选择是否进行**shuffle**过程。由参数**shuffle: Boolean = false/true**决定。
>
> **repartition**实际上是调用的**coalesce**，进行**shuffle**。源码如下：
>
> ```scala
> def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] = withScope {
> coalesce(numPartitions, shuffle = true)
> }
> ```
>
> 如果是减少分区，尽量避免shuffle。



## `sortBy(func,[ascending],[numTasks])`

作用: 使用**func**先对数据进行处理，按照处理后结果排序，默认升序。

```scala
val rdd1 = sc.parallelize(Array(1,3,4,10,4,6,9,20,30,16))
val rdd2 = rdd1.sortBy(x => x)
```

## `pipe(command,[envVars])`

作用: 管道，针对每个分区，把 RDD 中的每个数据通过管道传递给**shell**命令或脚本，返回输出的RDD。一个分区执行一次这个命令。

注意:脚本要放在 worker 节点可以访问到的位置

```scala
val rdd1 = sc.parallelize(Array(1,3,4,10,4,6,9,20,30,16))
val rdd2 = rdd1.pipe("src/main/resources/scripts.sh")
```

# 双 `value` 类型交互

这里的“双 **Value** 类型交互”是指的两个 **RDD[V]** 进行交互.

## `Union(otherDataset)`

作用：求并集. 对源 RDD 和参数 RDD 求并集后返回一个新的 RDD

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.union(rdd2)   # 等价于 val union_rdd = rdd1 ++ rdd2
```

## `subtract(otherDataset)`

作用: 计算差集. 从原 RDD 中减去 原 RDD 和 otherDataset 中的共同的部分

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.subtract(rdd2)
```

## `intersection(otherDataset)`

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.intersection(rdd2)
```

## `cartesian(otherDataset)`

作用: 计算 2 个 RDD 的笛卡尔积. 尽量避免使用

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.cartesian(rdd2)
```

## `zip(otherDataset)`

作用: 拉链操作. 需要注意的是, 在 Spark 中, 两个 RDD 的元素的数量和分区数`都`必须相同, 否则会抛出异常.(在 scala 中, 两个集合的长度可以不同)其实本质就是要求的每个分区的元素的数量相同.

```scala
val rdd1 = sc.parallelize(1 to 5)
val rdd2 = sc.parallelize( 11 to 15)
val zip_rdd = rdd1.zip(rdd2)
```

# Key-Value 类型

大多数的 Spark 操作可以用在任意类型的 RDD 上, 但是有一些比较特殊的操作只能用在**key-value**类型的 RDD 上。

这些特殊操作大多都涉及到 shuffle 操作, 比如: 按照 key 分组(group), 聚合(aggregate)等。在 Spark 中, 这些操作在包含对偶类型(**Tuple2**)的 RDD 上自动可用(通过隐式转换)。

```scala
object RDD {
  implicit def rddToPairRDDFunctions[K, V](rdd: RDD[(K, V)])
    (implicit kt: ClassTag[K], vt: ClassTag[V], ord: Ordering[K] = null): PairRDDFunctions[K, V] = {
    new PairRDDFunctions(rdd)
  }
```

键值对的操作是定义在**PairRDDFunctions**类上, 这个类是对**RDD[(K, V)]**的装饰。



## `partitionBy`

作用: 对 pairRDD 进行分区操作，如果原有的 partionRDD 的分区器和传入的分区器相同, 则返回原 pairRDD，否则会生成 ShuffleRDD，即会产生 **shuffle** 过程。

```scala partitionBy源码
def partitionBy(partitioner: Partitioner): RDD[(K, V)] = self.withScope {
  
  if (self.partitioner == Some(partitioner)) {
    self
  } else {
    new ShuffledRDD[K, V, V](self, partitioner)
  }
}
```

```scala
val rdd1 = sc.parallelize(Array((1, "a"), (2, "b"), (3, "c"), (4, "d")))
println(rdd1.partitions.length)
val rdd2 = rdd1.partitionBy(new org.apache.spark.HashPartitioner(4))
println(rdd2.partitions.length)
rdd2.collect.foreach(println)
```

## `reduceByKey(func,[numTasks])`

作用: 在一个**(K,V)**的 RDD 上调用，返回一个**(K,V)**的 RDD，使用指定的**reduce**函数，将相同**key**的**value**聚合到一起，**reduce**任务的个数可以通过第二个可选的参数来设置。

```scala
val rdd1 = sc.parallelize(List(("female",1),("male",5),("female",5),("male",2)))
val rdd2 = rdd1.reduceByKey(_+_)
```

## `groupByKey()`

作用: 按照**key**进行分组.

```scala
val rdd1 = sc.parallelize(Array("hello", "world", "nihao", "hello", "are", "go"))
val rdd2 = rdd1.map((_,1)).groupByKey()
rdd2.collect.foreach(println)
```

结果：

```
(are,CompactBuffer(1))
(world,CompactBuffer(1))
(hello,CompactBuffer(1, 1))
(go,CompactBuffer(1))
(nihao,CompactBuffer(1))
```

> 基于当前的实现, **groupByKey**必须在内存中持有所有的键值对，如果一个**key**有太多的**value**, 则会导致内存溢出(OutOfMemoryError)，所以这操作非常耗资源, 如果分组的目的是为了在每个**key**上执行聚合操作(比如: **sum** 和 **average**), 则应该使用**PairRDDFunctions.aggregateByKey** 或者**PairRDDFunctions.reduceByKey**, 因为他们有更好的性能(会先在分区进行预聚合)。



> reduceByKey 和 groupByKey的区别
>
> 1. **reduceByKey**：按照**key**进行聚合，在**shuffle**之前有**combine**（预聚合）操作，返回结果是**RDD[k,v]**。
> 2. **groupByKey**：按照**key**进行分组，直接进行**shuffle**。
> 3. r**educeByKey**比**groupByKey**性能更好，建议使用。

## `aggregateByKey(zeroValue)(seqOp, combOp, [numTasks])`

源码：

```scala
def aggregateByKey[U: ClassTag](zeroValue: U)(seqOp: (U, V) => U,
                                              combOp: (U, U) => U): RDD[(K, U)] = self.withScope {
    aggregateByKey(zeroValue, defaultPartitioner(self))(seqOp, combOp)
}
```

> 使用给定的 combine 函数和一个初始化的**zero value**, 对每个**key**的**value**进行聚合。
>
> 这个函数返回的类型**U**不同于源 RDD 中的**V**类型，**U**的类型是由初始化的**zero value**来定的。所以, 需要两个操作: 
>
> - 一个操作(**seqOp**)去把 1 个**v**变成 1 个**U** 
> - 另外一个操作(**combOp**)来合并 2 个**U**
>
> 第一个操作用于在一个分区进行合并, 第二个操作用在两个分区间进行合并。
>
> 为了避免内存分配, 这两个操作函数都允许返回第一个参数, 而不用创建一个新的**U**。
>
> 参数描述:
>
> **zeroValue**：给每一个分区中的每一个**key**一个初始值；
>
> **seqOp：**函数用于在每一个分区中用初始值逐步迭代**value；**
>
> **combOp：**函数用于合并每个分区中的结果。



创建一个 pairRDD，取出每个分区相同**key**对应值的最大值，然后相加：

```scala
val rdd1 = sc.parallelize(List(("a",3),("a",2),("c",4),("b",3),("c",6),("c",8)),2)
val rdd2 = rdd1.aggregateByKey(Int.MinValue)((u,v) => math.max(u,v),(u1,u2)=>u1+u2)
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920114701904.png)

## `foldByKey(zeroValue: V)(func: (V, V) => V)`

作用：**aggregateByKey**的简化操作，**seqop**和**combop**相同



```scala
val rdd1 = sc.parallelize(List(("a",3),("a",2),("c",4),("b",3),("c",6),("c",8)),2)
val rdd2 = rdd1.foldByKey(3)((u,v)=>u+v)
```

> groupByKey, reduceByKey, foldByKey,aggregateByKey的区别和联系?
>
> 1. groupByKey：按照key进行分组，**直接进行shuffle**
> 2. reduceByKey：聚合（所有的聚合算子都有预聚合），**分区内聚合和分区间聚合逻辑一样**,在shuffle之前有预聚合的操作，性能比groupByKey要好。
> 3. foldByKey：折叠，也是聚合，**分区内聚合和分区间聚合逻辑一样**。可以指定初始值，**初始值值只在分区内聚合有效,有多少个分区就参与了多少次运算。**
> 4. aggregateByKey：实现了分区内聚合和分区间聚合逻辑不一样，初始值在分区内聚合使用。

## `combineByKey`

作用: 针对每个**K**, 将**V**进行合并成**C**, 得到**RDD[(K,C)]**

参数描述:

- `createCombiner`:会遍历分区中的每个**key-value**对. 如果第一次碰到这个**key**, 则调用**createCombiner**函数,传入**value**, 得到一个**C**类型的值.(如果不是第一次碰到这个 key, 则不会调用这个方法)
- `mergeValue`:如果不是第一个遇到这个**key**, 则调用这个函数进行合并操作，分区内合并。
- `mergeCombiners`:跨分区合并相同的**key**的值(**C**)。

```scala 源码
def combineByKey[C](
                       createCombiner: V => C,
                       mergeValue: (C, V) => C,
                       mergeCombiners: (C, C) => C): RDD[(K, C)] = self.withScope {
    combineByKeyWithClassTag(createCombiner, mergeValue, mergeCombiners,
        partitioner, mapSideCombine, serializer)(null)
}
```

创建一个 **pairRDD**，根据 **key** 计算每种 **key** 的**value**的平均值。



```scala
val rdd1 = sc.parallelize(Array(("a", 88), ("b", 95), ("a", 91), ("b", 93), ("a", 95), ("b", 98)),2)
val rdd2 = rdd1.combineByKey((_,1),(accumulate:(Int,Int),v)=>(accumulate._1+v,accumulate._2+1),(u:(Int,Int),v:(Int,Int))=>((u._1+v._1), (u._2+v._2)))
rdd2.collect.foreach(println) // (b,(286,3))   (a,(274,3))
val rdd3 = rdd2.map(item => (item._1,item._2._1.toDouble / item._2._2))
rdd3.collect.foreach(println) // (b,95.33333333333333)   (a,91.33333333333333)
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920121845413.png)



## `sortByKey()`

作用: 在一个**(K,V)**的 RDD 上调用, **K**必须实现 **Ordered[K]** 接口(或者有一个隐式值: **Ordering[K]**), 返回一个按照**key**进行排序的**(K,V)**的 RDD

```scala
val rdd1 = sc.parallelize(Array((1, "a"), (10, "b"), (11, "c"), (4, "d"), (20, "d"), (10, "e")))
val rdd2 = rdd1.sortByKey()
```

## `mapValues()`

作用: 针对**(K,V)**形式的类型只对**V**进行操作

```scala
val rdd1 = sc.parallelize(Array((1, "a"), (10, "b"), (11, "c"), (4, "d"), (20, "d"), (10, "e")))
val rdd2 = rdd1.mapValues("<"+_+">")
```

## `join(otherDataset, [numTasks])`

内连接:在类型为**(K,V)**和**(K,W)**的 RDD 上调用，返回一个相同 key 对应的所有元素对在一起的**(K,(V,W))**的RDD。

```scala
val rdd1 = sc.parallelize(Array((1, "a"), (1, "b"), (2, "c")))
val rdd2 = sc.parallelize(Array((1, "aa"), (3, "bb"), (2, "cc")))
val rdd3 = rdd1.join(rdd2)
```

结果：

```
(1,(a,aa))
(1,(b,aa))
(2,(c,cc))
```

> 1. 如果某一个 RDD 有重复的 **Key**, 则会分别与另外一个 RDD 的相同的 **Key**进行组合。
> 2. 也支持外连接: `leftOuterJoin`, `rightOuterJoin`和`fullOuterJoin`。

## `cogroup(otherDataset, [numTasks])`

作用：在类型为**(K,V)**和**(K,W)**的 RDD 上调用，返回一个**(K,(Iterable<V>,Iterable<W>))**类型的 RDD

```scala
	val rdd1 = sc.parallelize(Array((1, 10),(2, 20),(1, 100),(3, 30)),1)
	val rdd2 = sc.parallelize(Array((1, "a"),(2, "b"),(1, "aa"),(3, "c")),1)
	val rdd3 = rdd1.cogroup(rdd2)
	rdd3.collect.foreach(println)
```

结果：

```
(1,(CompactBuffer(10, 100),CompactBuffer(a, aa)))
(3,(CompactBuffer(30),CompactBuffer(c)))
(2,(CompactBuffer(20),CompactBuffer(b)))
```





