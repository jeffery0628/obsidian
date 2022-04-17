---
Create: 2021年 十二月 1日, 星期三 09:39
tags: 
  - Engineering/spark
  - 大数据
---



# Value 类型

## map
函数签名：
```scala
def map[U: ClassTag](f: T => U): RDD[U] 
```
功能说明：参数f是一个函数，它可以接收一个参数。当某个RDD执行map方法时，会遍历该RDD中的每一个数据项，并依次应用f函数，从而产生一个新的RDD。即，这个新RDD中的每一个元素都是原来RDD中每一个元素依次应用f函数而得到的。

作用: 返回一个新的 RDD, 该 RDD 是由原 RDD 的每个元素经过函数转换后的值而组成. 就是对 RDD 中的数据做转换。

源码分析
```scala
def map[U: ClassTag](f: T => U): RDD[U] = withScope {
    val cleanF = sc.clean(f)
    new MapPartitionsRDD[U, T](this, (context, pid, iter) => iter.map(cleanF))
  }


private[spark] class MapPartitionsRDD[U: ClassTag, T: ClassTag](    
      ......    
      override def getPartitions: Array[Partition] = firstParent[T].partitions    
      ......
}

	
 protected[spark] def firstParent[U: ClassTag]: RDD[U] = {
    dependencies.head.rdd.asInstanceOf[RDD[U]]
  }

```

需求说明：创建一个1-4数组的RDD，两个分区，将所有元素*2形成新的RDD


```scala
// 3.1 创建一个RDD
val rdd = sc.makeRDD(1 to 4, 2)
//3.2 调用map方法，每个元素乘以2
val mapRdd= rdd.map((x:Int)=>{ x * 2})
//3.3 简化
val mapRdd= rdd.map(_ *  2)
```
![[700 Attachments/Pasted image 20220313143203.png]]
```scala
   def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4,2)

        // 3.2 调用map方法，每个元素乘以2
        val mapRdd: RDD[Int] = rdd.map(_ * 2)

        // 3.3 打印修改后的RDD中数据
        mapRdd.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
```

## mapPartitions
函数签名：
```scala
 def mapPartitions[U: ClassTag](
      f: Iterator[T] => Iterator[U],
      preservesPartitioning: Boolean = false): RDD[U]

```
功能说明：Map是一次处理一个元素，而mapPartitions一次处理一个分区数据。 f函数把每一个分区的数据分别放入到迭代器中，批处理。preservesPartitioning：是否保留上游RDD的分区信息，默认false。

作用：类似于`map(func)`，但是是独立在每个分区上运行，所以func的类型是 `Iterator<T> => Iterator<U>`

假设有`N`个元素，有`M`个分区，那么`map`的函数将被调用`N`次，而`mapPatitions`被调用`M`次，一个函数一次处理所有分区。

```scala
// 3.1 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)
// 3.1 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)
// 3.3 简化
val rdd1 = rdd.mapPartitions(datas=>datas.map(_*2))
```

![[700 Attachments/Pasted image 20220313143922.png]]
```scala
def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)

        // 3.2 调用mapPartitions方法，每个元素乘以2
        val rdd1 = rdd.mapPartitions(x=>x.map(_*2))

        // 3.3 打印修改后的RDD中数据
        rdd1.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
}
```


> `map()` 和 `mapPartitions()`的区别
>
> 1. `map()`:每次处理一条数据。
> 2. `mapPartitions()`:每次处理一个分区的数据，这个分区的数据处理完后，原RDD中该分区的数据才能释放，可能导致OOM。
> 3. 当内存空间较大的时候建议使用`mapPartitions()`，以提高处理效率。
> 4. ![[700 Attachments/Pasted image 20220313144043.png]]



## mapPartitionsWithIndex
函数签名：
```scala
def mapPartitionsWithIndex[U: ClassTag](
      f: (Int, Iterator[T]) => Iterator[U],  // Int表示分区编号
      preservesPartitioning: Boolean = false): RDD[U]

```

作用：和`mapPartitions(func)`类似，但是会给func多提供一个`Int`值来表示分区的索引。所以func的类型是`((Int,Iterator<T>)=>Iterator<U>)`

需求说明：创建一个RDD，使每个元素跟所在分区号形成一个元组，组成一个新的RDD

```scala
// 3.1 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)
// 3.2 使每个元素跟所在分区形成一个元组，组成一个新的RDD
val indexRdd= rdd.mapPartitionsWithIndex((index,items)=>{items.map((x)=>{(index,x)})})
//3.3 简化
val indexRdd = rdd.mapPartitionsWithIndex( (index,items)=>{items.map( (index,_) )} )

```
![[700 Attachments/Pasted image 20220313150100.png]]
```scala
def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc = new SparkContext(conf)

        //3具体业务逻辑
        // 3.1 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(1 to 4, 2)

        // 3.2 创建一个RDD，使每个元素跟所在分区号形成一个元组，组成一个新的RDD
        val indexRdd = rdd.mapPartitionsWithIndex( (index,items)=>{items.map( (index,_) )} )

        //扩展功能：第二个分区元素*2，其余分区不变
// 3.3 打印修改后的RDD中数据
        indexRdd.collect().foreach(println)

        //4.关闭连接
        sc.stop()
    }
```

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



## flatMap
函数签名：
```scala
def flatMap[U: ClassTag](f: T => TraversableOnce[U]): RDD[U]

```

作用：与map操作类似，将RDD中的每一个元素通过应用f函数依次转换为新的元素，并封装到RDD中。
区别：在flatMap操作中，f函数的返回值是一个集合，并且会将每一个该集合中的元素拆分出来放到新的RDD

需求说明：创建一个集合，集合里面存储的还是子集合，把所有子集合中数据取出放入到一个大的集合中。
```scala
    def main(args: Array[String]): Unit = {
      val conf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")
      val sc = new SparkContext(conf)
      // 3.1 创建一个RDD
      val listRdd = sc.makeRDD(List(List(1,2),List(3,4),List(5,6),List(7)),2)
      // 3.2 把所有子集合中数据取出放入到一个大的集合中
      val flatmapRdd = listRdd.flatMap(it => it)
      flatmapRdd.collect().foreach(println)
      sc.stop()
    }
```
![[700 Attachments/Pasted image 20220313151048.png]]


## glom
函数签名：
```scala
def glom(): RDD[Array[T]]
```

作用: 将每一个分区的元素合并成一个数组，形成新的 RDD 类型是`RDD[Array[T]]`

需求说明：创建一个2个分区的RDD，并将每个分区的数据放到一个数组，求出每个分区的最大值


```scala
// 创建一个2个分区的RDD，并将每个分区的数据放到一个数组，求出每个分区的最大值
val rdd = sc.makeRDD(1 to 4, 2)
val glomRdd = rdd.glom().map(_.max)
glomRdd.collect().foreach(println)
```
![[700 Attachments/Pasted image 20220313151905.png]]

## groupBy
函数签名：
```
def groupBy[K](f: T => K)(implicit kt: ClassTag[K]): RDD[(K, Iterable[T])]
```

作用：按照`func`的返回值进行分组。`func`返回值作为 `key`, 对应的值放入一个迭代器中。 返回的RDD类型是： `RDD[(K, Iterable[T])`。(即 (item,group idx))

需求说明：创建一个RDD，按照元素模以2的值进行分组

```scala
val rdd = sc.makeRDD(1 to 4,2)
val groupbyRdd = rdd.groupBy( _% 2)
```
![[700 Attachments/Pasted image 20220313153339.png]]
> groupBy会存在shuffle过程
> shuffle：将不同的分区数据进行打乱重组的过程

### groupby 之 wordcount
![[700 Attachments/Pasted image 20220313153518.png]]
```scala
def main(args: Array[String]): Unit = {  
  val conf: SparkConf = new SparkConf().setAppName("wordcount").setMaster("local[*]")  
  val sc: SparkContext = new SparkContext(conf)  
  
  // 3.1 创建一个RDD  
 val strList: List[String] = List("Hello scala", "Hello spark", "Hello hadoop")  
  val rdd: RDD[String] = sc.makeRDD(strList)  
  // 3.2 将字符串拆分成一个一个的单词  
 val flatmapRdd: RDD[String] = rdd.flatMap(_.split(" "))  
  // 3.3 将单词结果进行转换：word=>(word,1)  
 val mapRdd: RDD[(String, Int)] = flatmapRdd.map((_, 1))  
  // 3.4 将转换结构后的数据分组  
 val groupbyRdd: RDD[(String, Iterable[(String, Int)])] = mapRdd.groupBy(it => it._1)  
  // 3.5 将分组后的数据进行结构的转换  
 val wordCountRdd: RDD[(String, Int)] = groupbyRdd.map {  
    case (word, items) => {  
      (word, items.size)  
    }  
  }  
  wordCountRdd.collect().foreach(println)  
  
  sc.stop()  
}
```


## filter
函数签名：
```scala
def filter(f: T => Boolean): RDD[T]
```

作用：接收一个返回值为布尔类型的函数作为参数。当某个RDD调用filter方法时，会对该RDD中每一个元素应用f函数，如果返回值类型为true，则该元素会被添加到新的RDD中。

需求说明：需求说明：创建一个RDD（由字符串组成），过滤出一个新RDD（包含”xiao”子串）

```scala
val rdd1 = sc.parallelize(Array("xiaoli","laoli","laowang","xiaowang","xiaojing","xiaokong"))
val rdd2 = rdd1.filter(_.contains("xiao"))
rdd2.collect.foreach(println)
```

## sample
函数签名：
```scala
def sample(
	withReplacement: Boolean,// withReplacement表示：抽出的数据是否放回，true为有放回的抽样，false为无放回的抽样；
	fraction: Double,//当withReplacement=false时：选择每个元素的概率;取值一定是[0,1] ；当withReplacement=true时：选择每个元素的期望次数; 取值必须大于等于0。
	seed: Long = Utils.random.nextLong): RDD[T] // seed表示：指定随机数生成器种子。一般用默认的, 或者传入当前的`时间戳`。
)

```
作用:从大量的数据中采样,以指定的随机种子随机抽样出比例为**fraction**的数据，(抽取到的数量是: size \* fraction). 需要注意的是得到的结果并不能保证准确的比例.

需求说明：创建一个RDD（1-10），从中选择放回和不放回抽
```scala
// 3.1 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(1 to 10)

// 打印放回的抽样
val withreplaceRdd: RDD[Int] = rdd.sample(true, 0.4, 2)
withreplaceRdd.collect().foreach(println)

println("--------------")

// 打印不放回的抽样
val withoutReplaceRdd: RDD[Int] = rdd.sample(false, 0.2, 3)
withreplaceRdd.collect().foreach(println)
```

> 抽出的结果存在重复，因此结果的个数可能大于10，但能保证去重之后的元素是原来的一半。

## distinct
函数签名： 
```scala
def distinct(): RDD[T] // 默认情况下，distinct会生成与原RDD分区个数一致的分区数
```

作用:对 RDD 中元素执行去重操作，并将去重后的元素放到新的RDD中。参数表示任务的数量，默认值和分区数保持一致。

源码解析：
```scala
 def distinct(): RDD[T] = withScope {
    distinct(partitions.length)
  }

 def distinct(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] = withScope {
    map(x => (x, null)).reduceByKey((x, y) => x, numPartitions).map(_._1)
  }

```
例如：输入数据：1，2 ， 3 ， 4 ， 1 ， 2
![[700 Attachments/Pasted image 20220313161207.png]]
> 用分布式的方式去重比HashSet集合方式不容易OOM


```scala
val rdd1 = sc.parallelize(Array(10,10,2,5,3,5,3,6,9,1))
val rdd2 = rdd1.distinct
```

函数签名：
```scala
def distinct(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] // 可以去重后修改分区个数
```
```scala
// 3.3 对RDD采用多个Task去重，提高并发度
 distinctRdd.distinct(2).collect().foreach(println)
```
![[700 Attachments/Pasted image 20220313161454.png]]


## coalesce
Coalesce算子包括：配置执行Shuffle和配置不执行Shuffle两种方式。
### 不执行shuffle方式
函数签名：
```scala
def coalesce(numPartitions: Int, shuffle: Boolean = false,// shuffle为false，则该操作会将分区数较多的原始RDD向分区数比较少的目标RDD，进行转换。
               partitionCoalescer: Option[PartitionCoalescer] = Option.empty)
              (implicit ord: Ordering[T] = null) : RDD[T]

```
功能说明：缩减分区数，用于大数据集过滤后，提高小数据集的执行效率。
需求说明一：4个分区合并为2个分区
![[700 Attachments/Pasted image 20220313161833.png]]
需求说明二：3个分区合并为2个分区（数据容易产生数据倾斜）
![[700 Attachments/Pasted image 20220313161919.png]]
```scala
    def main(args: Array[String]): Unit = {

        //1.创建SparkConf并设置App名称
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

        //2.创建SparkContext，该对象是提交Spark App的入口
        val sc: SparkContext = new SparkContext(conf)

        //3.创建一个RDD
        //val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4), 4)

        //3.1 缩减分区
        //val coalesceRdd: RDD[Int] = rdd.coalesce(2)

        //4. 创建一个RDD
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6), 3)
        //4.1 缩减分区
        val coalesceRdd: RDD[Int] = rdd.coalesce(2)

        //5 打印查看对应分区数据
        val indexRdd: RDD[Int] = coalesceRdd.mapPartitionsWithIndex(
            (index, datas) => {
                // 打印每个分区数据，并带分区号
                datas.foreach(data => {
                    println(index + "=>" + data)
                })
                // 返回分区的数据
                datas
            }
        )
        indexRdd.collect()

        //6. 关闭连接
        sc.stop()
    }
```

### 执行shuffle
```scala
//3. 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5, 6), 3)
//3.1 执行shuffle
val coalesceRdd: RDD[Int] = rdd.coalesce(2, true)

```

shuffle原理：
![[700 Attachments/Pasted image 20220313162229.png]]


## repartition
函数签名： 
```scala
def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T]
```

作用: 该操作内部其实执行的是coalesce操作，参数shuffle的默认值为true。无论是将分区数多的RDD转换为分区数少的RDD，还是将分区数少的RDD转换为分区数多的RDD，repartition操作都可以完成，因为无论如何都会经shuffle过程。

需求说明：创建一个4个分区的RDD，对其重新分区。
![[700 Attachments/Pasted image 20220313162511.png]]
```scala
val rdd1 = sc.parallelize(1 to 100, 5)
val rdd2 = rdd1.repartition(3)
println(rdd2.partitions.length)
val rdd3 = rdd1.repartition(10)
println(rdd3.partitions.length)
```
###  `coalase`  和 `repartition` 的区别
1. coalesce重新分区，可以选择是否进行shuffle过程。由参数shuffle: Boolean = false/true决定。
2. epartition实际上是调用的coalesce，进行shuffle。源码如下：
	```scala
	def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] = withScope {
		coalesce(numPartitions, shuffle = true)
	}
	```

3. coalesce一般为缩减分区，如果扩大分区，不使用shuffle是没有意义的，repartition扩大分区执行shuffle。

## sortBy
函数签名：
```scala
def sortBy[K]( f: (T) => K,
			  ascending: Boolean = true,// 默认为正序排列
			  numPartitions: Int = this.partitions.length)
      (implicit ord: Ordering[K], ctag: ClassTag[K]): RDD[T]

```


作用: 该操作用于排序数据。在排序之前，可以将数据通过f函数进行处理，之后按照f函数处理的结果进行排序，默认为正序排列。排序后新产生的RDD的分区数与原RDD的分区数一致。

需求说明：创建一个RDD，按照不同的规则进行排序
```scala
// 3.1 创建一个RDD
val rdd: RDD[Int] = sc.makeRDD(List(2,1,3,4))

// 3.2 按照自身大小排序
rdd.sortBy(x=>x).collect().foreach(println)
println("--------------")
// 3.3 按照与3余数的大小排序
rdd.sortBy(x => x % 3).collect().foreach(println)

```
![[700 Attachments/Pasted image 20220313163124.png]]


## pipe

作用: 管道，针对每个分区，把 RDD 中的每个数据通过管道传递给**shell**命令或脚本，返回输出的RDD。一个分区执行一次这个命令。

注意:脚本要放在 worker 节点可以访问到的位置

```scala
val rdd1 = sc.parallelize(Array(1,3,4,10,4,6,9,20,30,16))
val rdd2 = rdd1.pipe("src/main/resources/scripts.sh")
```

# 双 `value` 类型交互

这里的“双 **Value** 类型交互”是指的两个 **RDD[V]** 进行交互.

## Union
函数签名：
```scala
def union(other: RDD[T]): RDD[T]
```
作用：求并集. 对源 RDD 和参数 RDD 求并集后返回一个新的 RDD
![[700 Attachments/Pasted image 20220313204654.png]]

需求说明：创建两个RDD，求并集

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.union(rdd2)   # 等价于 val union_rdd = rdd1 ++ rdd2
```
![[700 Attachments/Pasted image 20220313204757.png]]
## subtract
函数签名：
```scala
def subtract(other: RDD[T]): RDD[T]
```
作用: 计算差集. 从原 RDD 中减去 原 RDD 和 otherDataset 中的共同的部分
![[700 Attachments/Pasted image 20220313204858.png]]
需求说明：创建两个RDD，求第一个RDD与第二个RDD的差集
```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.subtract(rdd2)
```

![[700 Attachments/Pasted image 20220313205015.png]]

## intersection
函数签名： 
```scala
def intersection(other: RDD[T]): RDD[T]
```
作用：对源RDD和参数RDD求交集后返回一个新的RDD
![[700 Attachments/Pasted image 20220313205203.png]]
需求说明：创建两个RDD，求两个RDD的交集

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.intersection(rdd2)
```
![[700 Attachments/Pasted image 20220313205247.png]]

## cartesian

作用: 计算 2 个 RDD 的笛卡尔积. 尽量避免使用

```scala
val rdd1 = sc.parallelize(1 to 6)
val rdd2 = sc.parallelize( 4 to 10)
val union_rdd = rdd1.cartesian(rdd2)
```

## zip
函数签名： 
```scala
def zip[U: ClassTag](other: RDD[U]): RDD[(T, U)]
```
作用: 拉链操作. 需要注意的是, 在 Spark 中, 两个 RDD 的元素的数量和分区数`都`必须相同, 否则会抛出异常.(在 scala 中, 两个集合的长度可以不同)其实本质就是要求的每个分区的元素的数量相同.

需求说明：创建两个RDD，并将两个RDD组合到一起形成一个(k,v)RDD

```scala
val rdd1 = sc.parallelize(1 to 5)
val rdd2 = sc.parallelize( 11 to 15)
val zip_rdd = rdd1.zip(rdd2)
```
![[700 Attachments/Pasted image 20220313205441.png]]

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

## partitionBy
函数签名： 
```scala
def partitionBy(partitioner: Partitioner): RDD[(K, V)]
```
作用: 对 pairRDD 进行分区操作，如果原有的 partionRDD 的分区器和传入的分区器相同, 则返回原 pairRDD，否则会生成 ShuffleRDD，即会产生 **shuffle** 过程。
partitionBy源码
```scala 
def partitionBy(partitioner: Partitioner): RDD[(K, V)] = self.withScope {
  
  if (self.partitioner == Some(partitioner)) {
    self
  } else {
    new ShuffledRDD[K, V, V](self, partitioner)
  }
}
```
需求说明：创建一个3个分区的RDD，对其重新分区
```scala
val rdd1 = sc.parallelize(Array((1, "a"), (2, "b"), (3, "c"), (4, "d")))
println(rdd1.partitions.length)
val rdd2 = rdd1.partitionBy(new org.apache.spark.HashPartitioner(4))
println(rdd2.partitions.length)
rdd2.collect.foreach(println)
```
![[700 Attachments/Pasted image 20220313211036.png]]
### HashPartitioner 源码解读
```scala
class HashPartitioner(partitions: Int) extends Partitioner {
    require(partitions >= 0, s"Number of partitions ($partitions) cannot be negative.")
    
    def numPartitions: Int = partitions
    
    def getPartition(key: Any): Int = key match {
        case null => 0
        case _ => Utils.nonNegativeMod(key.hashCode, numPartitions)
    }
    
    override def equals(other: Any): Boolean = other match {
        case h: HashPartitioner =>
            h.numPartitions == numPartitions
        case _ =>
            false
    }
    
    override def hashCode: Int = numPartitions
}
```

### 自定义分区器
```scala
// 自定义分区
class MyPartitioner(num: Int) extends Partitioner {
    // 设置的分区数
    override def numPartitions: Int = num

    // 具体分区逻辑
    override def getPartition(key: Any): Int = {

        if (key.isInstanceOf[Int]) {

            val keyInt: Int = key.asInstanceOf[Int]
            if (keyInt % 2 == 0)
                0
            else
                1
        }else{
            0
        }
    }
}

```



## reduceByKey
函数签名：
```scala
def reduceByKey(func: (V, V) => V): RDD[(K, V)]
def reduceByKey(func: (V, V) => V, numPartitions: Int): RDD[(K, V)]
```

作用: 该操作可以将RDD\[K,V\]中的元素==按照相同的K对V进行聚合==。其存在多种重载形式，还可以设置新RDD的分区数。

需求说明：统计单词出现次数
```scala
def main(args: Array[String]): Unit = {  
  val conf: SparkConf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")  
  val sc: SparkContext = new SparkContext(conf)  
  //3.1 创建第一个RDD  
 val rdd: RDD[(String, Int)] = sc.makeRDD(List(("a",1),("b",5),("a",5),("b",2)))  
  val reducebykeyRdd: RDD[(String, Int)] = rdd.reduceByKey((x, y) => x + y)  
  
  reducebykeyRdd.collect().foreach(println)  
}
```
![[700 Attachments/Pasted image 20220313212049.png]]

## groupByKey
函数签名：  
```scala
def groupByKey(): RDD[(K, Iterable[V])]
```

作用: 按照**key**进行分组。groupByKey对每个key进行操作，但只生成一个seq，并不进行聚合。该操作可以指定分区器或者分区数（默认使用HashPartitioner）

需求描述：创建一个pairRDD，将相同key对应值聚合到一个seq中，并计算相同key对应值的相加结果。

```scala
  def main(args: Array[String]): Unit = {
    val conf: SparkConf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")
    val sc: SparkContext = new SparkContext(conf)
    val rdd: RDD[(String, Int)] = sc.makeRDD(List(("a", 1), ("b", 5), ("a", 5), ("b", 2)))
    val groupbyRdd: RDD[(String, Iterable[Int])] = rdd.groupByKey()
    val mapRdd: RDD[(String, Int)] = groupbyRdd.map(t => (t._1, t._2.sum))
    mapRdd.collect().foreach(println)
    sc.stop()
  }
```
![[700 Attachments/Pasted image 20220313212940.png]]

需求说明：创建一个RDD，将相同key对应值聚合到一个seq中

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
> 3. **reduceByKey**比**groupByKey**性能更好，建议使用。
> 4. 开发指导：在不影响业务逻辑的前提下，优先选用reduceByKey。求和操作不影响业务逻辑，==求平均值影响业务逻辑==。

## aggregateByKey
函数签名：
```scala
def aggregateByKey[U: ClassTag](zeroValue: U)(seqOp: (U, V) => U,  combOp: (U, U) => U): RDD[(K, U)]
```
1. zeroValue（初始值）：给每一个分区中的每一种key一个初始值；
2. seqOp（分区内）：函数用于在每一个分区中用初始值逐步迭代value；
3. combOp（分区间）：函数用于合并每个分区中的结果。
源码：
```scala
def aggregateByKey[U: ClassTag](zeroValue: U)(
	seqOp: (U, V) => U,
	combOp: (U, U) => U): RDD[(K, U)] = 
	self.withScope {
    	aggregateByKey(zeroValue, defaultPartitioner(self))(seqOp, combOp)
	}
```
需求描述：取出每个分区相同key对应值的最大值，然后相加
```scala
def main(args: Array[String]): Unit = {  
  val conf: SparkConf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")  
  val sc: SparkContext = new SparkContext(conf)  
  // 需求描述：取出每个分区相同key对应值的最大值，然后相加  
 val list: List[(String, Int)] = List(("a",3),("a",2),("c",4),("b",3),("c",6),("c",8))  
  val rdd: RDD[(String, Int)] = sc.makeRDD(list,2)  
  val aggregatebykeyRdd: RDD[(String, Int)] = rdd.aggregateByKey(0)(math.max, _ + _)  
  aggregatebykeyRdd.collect().foreach(println)  
  sc.stop()  
}
```
![[700 Attachments/Pasted image 20220313214901.png]]

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


## foldByKey
函数签名：
```scala
def foldByKey(zeroValue: V)(func: (V, V) => V): RDD[(K, V)]
```
1. 参数zeroValue：是一个初始化值，它可以是任意类型
2. 参数func：是一个函数，分区内和分区间的操作相同

作用：aggregateByKey的简化操作，seqop和combop相同。即，分区内逻辑和分区间逻辑相同。

需求说明：需求说明：求wordcount

```scala
    // 创建一个RDD
    val list: List[(String, Int)] = List(("a",1),("a",1),("a",1),("b",1),("b",1),("b",1),("b",1),("a",1))
    val rdd: RDD[(String, Int)] = sc.makeRDD(list,2)
    val aggergatebykeyRdd: RDD[(String, Int)] = rdd.aggregateByKey(0)((u, v) => u + v, (u1, u2) => (u1 + u2))
    aggergatebykeyRdd.collect().foreach(println)
    print("-----------")
    val foldbykeyRdd: RDD[(String, Int)] = rdd.foldByKey(0)(_ + _)
    foldbykeyRdd.collect().foreach(println)
```

> groupByKey, reduceByKey, foldByKey,aggregateByKey的区别和联系?
>
> 1. groupByKey：按照key进行分组，**直接进行shuffle**
> 2. reduceByKey：聚合（所有的聚合算子都有预聚合），**分区内聚合和分区间聚合逻辑一样**,在shuffle之前有预聚合的操作，性能比groupByKey要好。
> 3. foldByKey：折叠，也是聚合，**分区内聚合和分区间聚合逻辑一样**。可以指定初始值，**初始值值只在分区内聚合有效,有多少个分区就参与了多少次运算。**
> 4. aggregateByKey：实现了分区内聚合和分区间聚合逻辑不一样，初始值在分区内聚合使用。

## combineByKey
函数签名： 
```scala
def combineByKey[C](
	createCombiner: V => C,
	mergeValue: (C, V) => C,
	mergeCombiners: (C, C) => C): RDD[(K, C)]
```

- `createCombiner`:会遍历分区中的每个**key-value**对. 如果第一次碰到这个**key**, 则调用**createCombiner**函数,传入**value**, 得到一个**C**类型的值.(如果不是第一次碰到这个 key, 则不会调用这个方法)
- `mergeValue`:如果不是第一个遇到这个**key**, 则调用这个函数进行合并操作，分区内合并。
- `mergeCombiners`:跨分区合并相同的**key**的值(**C**)。
作用: 针对每个**K**, 将**V**进行合并成**C**, 得到**RDD\[(K,C)\]**


```scala 源码
def combineByKey[C](
                       createCombiner: V => C,
                       mergeValue: (C, V) => C,
                       mergeCombiners: (C, C) => C): RDD[(K, C)] = 
self.withScope {
    combineByKeyWithClassTag(createCombiner, mergeValue, mergeCombiners,
        partitioner, mapSideCombine, serializer)(null)
}
```

创建一个 **pairRDD**，根据 **key** 计算每种 **key** 的**value**的平均值。
```scala
def main(args: Array[String]): Unit = {  
  val conf: SparkConf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")  
  val sc: SparkContext = new SparkContext(conf)  
  val list: List[(String, Int)] = List(("a", 88), ("b", 95), ("a", 91), ("b", 93), ("a", 95), ("b", 98))  
  val rdd: RDD[(String, Int)] = sc.makeRDD(list, 2)  
  val combinebykeyRdd: RDD[(String, (Int, Int))] = rdd.combineByKey((_, 1), (accu: (Int, Int), v: Int) => (accu._1 + v, accu._2 + 1), (x: (Int, Int), y: (Int, Int)) => (x._1 + y._1, x._2 + y._2))  
  val mapRdd: RDD[(String, Double)] = combinebykeyRdd.map(item => (item._1, item._2._1.toDouble / item._2._2))  
  mapRdd.collect().foreach(println)  
  sc.stop()  
}
```
![[700 Attachments/Pasted image 20220313222837.png]]


## sortByKey

函数签名： 
```scala
def sortByKey(
	ascending: Boolean = true,
	numPartitions: Int = self.partitions.length)  : RDD[(K, V)]
```

作用: 在一个**(K,V)**的 RDD 上调用, **K**必须实现 **Ordered[K]** 接口(或者有一个隐式值: **Ordering[K]**), 返回一个按照**key**进行排序的**(K,V)**的 RDD

需求说明：创建一个pairRDD，按照key的正序和倒序进行排序

```scala
def main(args: Array[String]): Unit = {  
  val conf: SparkConf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")  
  val sc: SparkContext = new SparkContext(conf)  
  
  // 创建一个RDD  
 val rdd: RDD[(Int, String)] = sc.makeRDD(Array((3,"aa"),(6,"cc"),(2,"bb"),(1,"dd")))  
  // 按照key的正序排序  
 val sortbykeyRdd1: RDD[(Int, String)] = rdd.sortByKey(true)  
  // 按照key的倒序  
 val sortbykeyRdd2: RDD[(Int, String)] = rdd.sortByKey(false)  
  
  sortbykeyRdd1.collect().foreach(println)  
  
  sc.stop()  
}
```
## mapValues
函数签名： 
```scala
def mapValues[U](f: V => U): RDD[(K, U)]
```

作用: 针对**(K,V)**形式的类型只对**V**进行操作

需求说明：创建一个pairRDD，并将value添加字符串"<>"

```scala
val rdd1 = sc.parallelize(Array((1, "a"), (10, "b"), (11, "c"), (4, "d"), (20, "d"), (10, "e")))
val rdd2 = rdd1.mapValues("<"+_+">")
```

## join
函数签名： 
```scala
def join[W](other: RDD[(K, W)]): RDD[(K, (V, W))]
def join[W](other: RDD[(K, W)], numPartitions: Int): RDD[(K, (V, W))] 
```
功能说明：（内连接）在类型为(K,V)和(K,W)的RDD上调用，返回一个相同key对应的所有元素对在一起的(K,(V,W))的RDD

需求说明：创建两个pairRDD，并将key相同的数据聚合到一个元组。


```scala
def main(args: Array[String]): Unit = {  
  val conf: SparkConf = new SparkConf().setAppName("sparkcore").setMaster("local[*]")  
  val sc: SparkContext = new SparkContext(conf)  
  val rdd1: RDD[(Int, String)] = sc.makeRDD(Array((1, "a"), (2, "b"), (3, "c")))  
  val rdd2 = sc.parallelize(Array((1, "aa"), (3, "bb"), (2, "cc"),(4,"dd"),(2,"mm")))  
  val rdd3 = rdd1.join(rdd2)  
  rdd3.collect().foreach(println)  
  sc.stop()  
}
```
结果：
```
(1,(a,aa))
(2,(b,cc))
(2,(b,mm))
(3,(c,bb))
```

> 1. 如果某一个 RDD 有重复的 **Key**, 则会分别与另外一个 RDD 的相同的 **Key**进行组合。
> 2. 也支持外连接: `leftOuterJoin`, `rightOuterJoin`和`fullOuterJoin`。
> 注意：如果Key只是某一个RDD有，这个Key不会关联


## cogroup
函数签名： 
```scala
def cogroup[W](other: RDD[(K, W)]): RDD[(K, (Iterable[V], Iterable[W]))]
```
作用：在类型为(K,V)和(K,W)的RDD上调用，返回一个(K,(Iterable\<V\>,Iterable\<W\>))类型的RDD

需求说明：创建两个pairRDD，并将key相同的数据聚合到一个迭代器。


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

![[700 Attachments/Pasted image 20220313224949.png]]





