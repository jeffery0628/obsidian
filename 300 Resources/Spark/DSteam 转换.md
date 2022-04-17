---
Create: 2022年 三月 16日, 星期三 10:30
tags: 
  - Engineering/spark
  - 大数据
---


DStream上的操作与RDD的类似，分为Transformations（转换）和Output Operations（输出）两种，此外转换操作中还有一些比较特殊的算子，如：updateStateByKey()、transform()以及各种Window相关的算子。

# 无状态转化操作
无状态转化操作就是把简单的RDD转化操作应用到每个批次上，也就是转化DStream中的每一个RDD。
部分无状态转化操作列在了下表中：
![[700 Attachments/Pasted image 20220316131259.png]]

需要记住的是，尽管这些函数看起来像作用在整个流上一样，但事实上每个DStream在内部是由许多RDD（批次）组成，且无状态转化操作是分别应用到每个RDD上的。

例如：reduceByKey()会归约每个时间区间中的数据，但不会归约不同区间之间的数据。在之前的wordcount程序中，我们只会统计几秒内接收到的数据的单词个数，而不会累加。

## Transform
Transform允许DStream上执行任意的RDD-to-RDD函数。即使这些函数并没有在DStream的API中暴露出来，通过该函数可以方便的扩展Spark API。该函数每一批次调度一次。其实也就是对DStream中的RDD应用转换。

```scala
  def main(args: Array[String]): Unit = {
    //创建SparkConf
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("WordCount")
    //创建StreamingContext
    val ssc = new StreamingContext(conf, Seconds(3))
    //创建DStream
    val lineDStream: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 9999)
    //转换为RDD操作
    val wordAndCountDStream: DStream[(String, Int)] = lineDStream.transform(rdd => {
      val words: RDD[String] = rdd.flatMap(_.split(" "))
      val wordAndOne: RDD[(String, Int)] = words.map((_, 1))
      val value: RDD[(String, Int)] = wordAndOne.reduceByKey(_ + _)
      value
    })
    //打印
    wordAndCountDStream.print
    //启动
    ssc.start()
    ssc.awaitTermination()
  }
```

# 有状态转化操作
## UpdateStateByKey
UpdateStateByKey算子用于将历史结果应用到当前批次，该操作允许在使用新信息不断更新状态的同时能够保留他的状态。

有时，我们需要在DStream中跨批次维护状态(例如流计算中累加wordcount)。针对这种情况，updateStateByKey()为我们提供了对一个状态变量的访问，用于键值对形式的DStream。给定一个由(键，事件)对构成的 DStream，并传递一个指定如何根据新的事件更新每个键对应状态的函数，它可以构建出一个新的 DStream，其内部数据为(键，状态) 对。
UpdateStateByKey() 的结果会是一个新的DStream，其内部的RDD 序列是由每个时间区间对应的(键，状态)对组成的。

为使用这个功能，需要做下面两步：
1. 定义状态，状态可以是一个任意的数据类型。
2. 定义状态更新函数，用此函数阐明如何使用之前的状态和来自输入流的新值对状态进行更新。

使用updateStateByKey需要对检查点目录进行配置，会使用检查点来保存状态。

![[700 Attachments/Pasted image 20220316225919.png]]

```scala
  def main(args: Array[String]): Unit = {
    //创建SparkConf
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("WordCount")
    //创建StreamingContext
    val ssc = new StreamingContext(conf, Seconds(3))
    //设置检查点路径  用于保存状态
    ssc.checkpoint("D:\\dev\\workspace\\my-bak\\spark-bak\\cp")
    //创建DStream
    val lineDStream: ReceiverInputDStream[String] = ssc.socketTextStream("localhost", 9999)
    //扁平映射
    val flatMapDS: DStream[String] = lineDStream.flatMap(_.split(" "))
    //结构转换
    val mapDS: DStream[(String, Int)] = flatMapDS.map((_,1))
    //聚合
    // 注意：DStreasm中reduceByKey只能对当前采集周期（窗口）进行聚合操作，没有状态
    //val reduceDS: DStream[(String, Int)] = mapDS.reduceByKey(_+_)
    val stateDS: DStream[(String, Int)] = mapDS.updateStateByKey(
      (seq: Seq[Int], state: Option[Int]) => {
        Option(seq.sum + state.getOrElse(0))
      }
    )
    //打印输出
    stateDS.print()
    //启动
    ssc.start()
    ssc.awaitTermination()
  }
```

## 窗口操作
Spark Streaming 也提供了窗口计算, 允许执行转换操作作用在一个窗口内的数据。
默认情况下, 计算只对一个时间段内的RDD进行, 有了窗口之后, 可以把计算应用到一个指定的窗口内的所有 RDD 上。一个窗口可以包含多个时间段，基于窗口的操作会在一个比StreamingContext的批次间隔更长的时间范围内，通过整合多个批次的结果，计算出整个窗口的结果。所有基于窗口的操作都需要两个参数，分别为窗口时长以及滑动步长。
- 窗口时长：计算内容的时间范围；
- 滑动步长：隔多久触发一次计算。
注意：这两者都必须为采集周期的整数倍。
如下图所示WordCount案例：窗口大小为批次的2倍，滑动步等于批次大小。
![[700 Attachments/Pasted image 20220316230328.png]]

需求：WordCount统计 3秒一个批次，窗口6秒，滑步3秒。

```scala
  def main(args: Array[String]): Unit = {
    //创建SparkConf
    val sparkConf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("WordCount")
    //创建StreamingContext
    val ssc = new StreamingContext(sparkConf, Seconds(3))
    //设置检查点路径  用于保存状态
    ssc.checkpoint("D:\\dev\\workspace\\my-bak\\spark-bak\\cp")
    //创建DStream
    val lineDStream: ReceiverInputDStream[String] = ssc.socketTextStream("hadoop202", 9999)
    //扁平映射
    val flatMapDS: DStream[String] = lineDStream.flatMap(_.split(" "))
    //设置窗口大小，滑动的步长
    val windowDS: DStream[String] = flatMapDS.window(Seconds(6),Seconds(3))
    //结构转换
    val mapDS: DStream[(String, Int)] = windowDS.map((_,1))
    //聚合
    val reduceDS: DStream[(String, Int)] = mapDS.reduceByKey(_+_)
    reduceDS.print()
    //启动
    ssc.start()
    ssc.awaitTermination()
  }
```


## 关于窗口的一些操作
1. window(windowLength, slideInterval)
	基于对源DStream窗化的批次进行计算返回一个新的Dstream
2. countByWindow(windowLength, slideInterval)
	返回一个滑动窗口计数流中的元素个数
3. countByValueAndWindow()
	返回的DStream则包含窗口中每个值的个数
4. reduceByWindow(func, windowLength, slideInterval)
	通过使用自定义函数整合滑动区间流元素来创建一个新的单元素流
5. reduceByKeyAndWindow(func, windowLength, slideInterval, [numTasks])
	当在一个(K,V)对的DStream上调用此函数，会返回一个新(K,V)对的DStream，此处通过对滑动窗口中批次数据使用reduce函数来整合每个key的value值
6. reduceByKeyAndWindow(func, invFunc, windowLength, slideInterval, [numTasks])
	这个函数是上述函数的变化版本，每个窗口的reduce值都是通过用前一个窗的reduce值来递增计算。通过reduce进入到滑动窗口数据并”反向reduce”离开窗口的旧数据来实现这个操作。如果把3秒的时间窗口当成一个池塘，池塘每一秒都会有鱼游进或者游出，那么第一个函数表示每由进来一条鱼，就在该类鱼的数量上累加。而第二个函数是，每由出去一条鱼，就将该鱼的总数减去一。