---
Create: 2022年 三月 15日, 星期二 22:54
tags: 
  - Engineering/spark
  - 大数据
---
# WordCount 实操
需求：使用netcat工具向9999端口不断的发送数据，通过SparkStreaming读取端口数据并统计不同单词出现的次数

## 添加依赖
```xml
<dependency>
	<groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
```

## 编写代码
```scala
  def main(args: Array[String]): Unit = {
    //创建配置文件对象 注意：Streaming程序至少不能设置为local，至少需要2个线程
    val conf: SparkConf = new SparkConf().setAppName("Spark01_W").setMaster("local[*]")
    //创建Spark Streaming上下文环境对象
    val ssc = new StreamingContext(conf,Seconds(3))
    //操作数据源-从端口中获取一行数据
    val socketDS: ReceiverInputDStream[String] = ssc.socketTextStream("localhost",9999)
    //对获取的一行数据进行扁平化操作
    val flatMapDS: DStream[String] = socketDS.flatMap(_.split(" "))
    //结构转换
    val mapDS: DStream[(String, Int)] = flatMapDS.map((_,1))
    //对数据进行聚合
    val reduceDS: DStream[(String, Int)] = mapDS.reduceByKey(_+_)
    //输出结果   注意：调用的是DS的print函数
    reduceDS.print()
    //启动采集器
    ssc.start()
    //默认情况下，上下文对象不能关闭
    //ssc.stop()
    //等待采集结束，终止上下文环境对象
    ssc.awaitTermination()
  }

```

## 启动程序并通过NetCat发送数据
```bash
nc -lk 9999
```
> 如果程序运行时，log日志太多，可以将spark conf目录下的log4j文件里面的日志级别改成WARN。

# WordCount解析

Discretized Stream是Spark Streaming的基础抽象，代表持续性的数据流和经过各种Spark算子操作后的结果数据流。在内部实现上，DStream是一系列连续的RDD来表示,每个RDD含有一段时间间隔内的数据，对这些 RDD的转换是由Spark引擎来计算的， DStream的操作隐藏的大多数的细节, 然后给开发者提供了方便使用的高级 API如下图：
![[700 Attachments/Pasted image 20220315230124.png]]


# 几点注意
1. 一旦StreamingContext已经启动, 则不能再添加新的 streaming computations
2. 一旦一个StreamingContext已经停止(StreamingContext.stop()), 他也不能再重启
3. 在一个 JVM 内, 同一时间只能启动一个StreamingContext
4. stop() 的方式停止StreamingContext, 也会把SparkContext停掉. 如果仅仅想停止StreamingContext, 则应该这样: stop(false)
5. 一个SparkContext可以重用去创建多个StreamingContext, 前提是以前的StreamingContext已经停掉,并且SparkContext没有被停掉



