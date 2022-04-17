---
Create: 2022年 三月 16日, 星期三 10:06
tags: 
  - Engineering/spark
  - 大数据
---
# RDD队列
测试过程中，可以通过使用ssc.queueStream(queueOfRDDs)来创建DStream，每一个推送到这个队列中的RDD，都会作为一个DStream处理。

## 案例实操
==需求==：循环创建几个RDD，将RDD放入队列。通过SparkStream创建Dstream，计算WordCount

```scala
  def main(args: Array[String]): Unit = {
    // 创建Spark配置信息对象
    val conf = new SparkConf().setMaster("local[*]").setAppName("RDDStream")
    // 创建SparkStreamingContext
    val ssc = new StreamingContext(conf, Seconds(3))
    // 创建RDD队列
    val rddQueue = new mutable.Queue[RDD[Int]]()
    // 创建QueueInputDStream
    val inputStream = ssc.queueStream(rddQueue,oneAtATime = false)
    // 处理队列中的RDD数据
    val mappedStream = inputStream.map((_,1))
    val reducedStream = mappedStream.reduceByKey(_ + _)
    // 打印结果
    reducedStream.print()
    // 启动任务
    ssc.start()
    // 循环创建并向RDD队列中放入RDD
    for (_ <- 1 to 5) {
      rddQueue += ssc.sparkContext.makeRDD(1 to 5, 10)
      Thread.sleep(2000)
    }
    ssc.awaitTermination()
  }

```

输出：
```
 

-------------------------------------------

Time: 1582192449000 ms

-------------------------------------------

(1,1)

(2,1)

(3,1)

(4,1)

(5,1)

-------------------------------------------

Time: 1582192452000 ms

-------------------------------------------

(1,2)

(2,2)

(3,2)

(4,2)

(5,2)

-------------------------------------------

Time: 1582192455000 ms

-------------------------------------------

(1,1)

(2,1)

(3,1)

(4,1)

(5,1)

-------------------------------------------

Time: 1582192458000 ms

-------------------------------------------

(1,1)

(2,1)

(3,1)

(4,1)

(5,1)

-------------------------------------------

Time: 1582192461000 ms

-------------------------------------------
```


# 自定义数据源
## 用法
需要继承Receiver，并实现onStart、onStop方法来自定义数据源采集。
## 案例实操
需求：自定义数据源，实现监控某个端口号，获取该端口号内容。

自定义数据源：
```scala
class MyReceiver(host: String, port: Int) extends Receiver[String](StorageLevel.MEMORY_ONLY) {  
  //创建一个Socket  
 private var socket: Socket = _  
  
  //最初启动的时候，调用该方法，作用为：读数据并将数据发送给Spark  
 override def onStart(): Unit = {  
    new Thread("Socket Receiver") {  
      setDaemon(true)  
      override def run() { receive() }  
    }.start()  
  }  
  
  //读数据并将数据发送给Spark  
 def receive(): Unit = {  
    try {  
      socket = new Socket(host, port)  
      //创建一个BufferedReader用于读取端口传来的数据  
 val reader = new BufferedReader(  
        new InputStreamReader(  
          socket.getInputStream, StandardCharsets.UTF_8))  
      //定义一个变量，用来接收端口传过来的数据  
 var input: String = null  
  
 //读取数据 循环发送数据给Spark 一般要想结束发送特定的数据 如："==END=="  
 while ((input = reader.readLine())!=null) {  
        store(input)  
      }  
    } catch {  
      case e: ConnectException =>  
        restart(s"Error connecting to $host:$port", e)  
        return  
 }  
  }  
  
  override def onStop(): Unit = {  
    if(socket != null ){  
      socket.close()  
      socket = null  
 }  
  }  
}
```

使用自定义的数据源采集数据
```scala
def main(args: Array[String]): Unit = {  
  //1.初始化Spark配置信息  
 val sparkConf = new SparkConf().setMaster("local[*]").setAppName("StreamWordCount")  
  //2.初始化SparkStreamingContext  
 val ssc = new StreamingContext(sparkConf, Seconds(5))  
  //3.创建自定义receiver的Streaming  
 val lineStream = ssc.receiverStream(new MyReceiver("hadoop202", 9999))  
  //4.将每一行数据做切分，形成一个个单词  
 val wordStream = lineStream.flatMap(_.split("\t"))  
  //5.将单词映射成元组（word,1）  
 val wordAndOneStream = wordStream.map((_, 1))  
  //6.将相同的单词次数做统计  
 val wordAndCountStream = wordAndOneStream.reduceByKey(_ + _)  
  //7.打印  
 wordAndCountStream.print()  
  //8.启动SparkStreamingContext  
 ssc.start()  
  ssc.awaitTermination()  
}

```

# Kafka 数据源
## 版本
ReceiverAPI：需要一个专门的Executor去接收数据，然后发送给其他的Executor做计算。
>存在的问题，接收数据的Executor和计算的Executor速度会有所不同，特别在接收数据的Executor速度大于计算的Executor速度，会导致计算数据的节点内存溢出。

DirectAPI：是由计算的Executor来主动消费Kafka的数据，速度由自身控制。

## Kafka 0-8 Receive模式
需求：通过SparkStreaming从Kafka读取数据，并将读取过来的数据做简单计算，最终打印到控制台。

导入依赖
```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming-kafka-0-8_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
```

编写代码：
0-8Receive模式，offset维护在zk中，程序停止后，继续生产数据，再次启动程序，仍然可以继续消费。可通过get /consumers/bigdata/offsets/主题名/分区号 查看
```scala
  def main(args: Array[String]): Unit = {

    //1.创建SparkConf
    val sparkConf: SparkConf = new SparkConf().setAppName("Spark04_ReceiverAPI").setMaster("local[*]")

    //2.创建StreamingContext
    val ssc = new StreamingContext(sparkConf, Seconds(3))

    //3.使用ReceiverAPI读取Kafka数据创建DStream
    val kafkaDStream: ReceiverInputDStream[(String, String)] = KafkaUtils.createStream(ssc,
      "hadoop202:2181,hadoop203:2181,hadoop204:2181",
      "bigdata",
      //v表示的主题的分区数
      Map("mybak" -> 2))

    //4.计算WordCount并打印        new KafkaProducer[String,String]().send(new ProducerRecord[]())
    val lineDStream: DStream[String] = kafkaDStream.map(_._2)
    val word: DStream[String] = lineDStream.flatMap(_.split(" "))
    val wordToOneDStream: DStream[(String, Int)] = word.map((_, 1))
    val wordToCountDStream: DStream[(String, Int)] = wordToOneDStream.reduceByKey(_ + _)
    wordToCountDStream.print()

    //5.开启任务
    ssc.start()
    ssc.awaitTermination()
  }

```


## Kafka 0-8 Direct模式
需求：通过SparkStreaming从Kafka读取数据，并将读取过来的数据做简单计算，最终打印到控制台。

导入依赖：
```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming-kafka-0-8_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
```
编写代码（自动维护offset1）
offset维护在checkpoint中，但是获取StreamingContext的方式需要改变，目前这种方式会丢失消息

```scala
  def main(args: Array[String]): Unit = {

    //1.创建SparkConf
    val sparkConf: SparkConf = new SparkConf().setAppName("Spark05_DirectAPI_Auto01").setMaster("local[*]")

    //2.创建StreamingContext
    val ssc = new StreamingContext(sparkConf, Seconds(3))

    ssc.checkpoint("D:\\dev\\workspace\\my-bak\\spark-bak\\cp")

    //3.准备Kafka参数
    val kafkaParams: Map[String, String] = Map[String, String](
      ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG -> "hadoop202:9092,hadoop203:9092,hadoop204:9092",
      ConsumerConfig.GROUP_ID_CONFIG -> "bigdata"
    )

    //4.使用DirectAPI自动维护offset的方式读取Kafka数据创建DStream
    val kafkaDStream: InputDStream[(String, String)] = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc,
      kafkaParams,
      Set("mybak"))

    //5.计算WordCount并打印
    kafkaDStream.map(_._2)
      .flatMap(_.split(" "))
      .map((_, 1))
      .reduceByKey(_ + _)
      .print()

    //6.开启任务
    ssc.start()
    ssc.awaitTermination()
  }

```

编写代码（自动维护offset2）
offset维护在checkpoint中，获取StreamingContext为getActiveOrCreate
这种方式缺点：
1. checkpoint小文件过多
2. checkpoint记录最后一次时间戳，再次启动的时候会把间隔时间的周期再执行一次

```scala
  def main(args: Array[String]): Unit = {
    val ssc: StreamingContext = StreamingContext.getActiveOrCreate("D:\\dev\\workspace\\my-bak\\spark-bak\\cp", () => getStreamingContext)

    ssc.start()
    ssc.awaitTermination()
  }

  def getStreamingContext: StreamingContext = {
    //1.创建SparkConf
    val sparkConf: SparkConf = new SparkConf().setAppName("DirectAPI_Auto01").setMaster("local[*]")

    //2.创建StreamingContext
    val ssc = new StreamingContext(sparkConf, Seconds(3))
    ssc.checkpoint("D:\\dev\\workspace\\my-bak\\spark-bak\\cp")

    //3.准备Kafka参数
    val kafkaParams: Map[String, String] = Map[String, String](
      ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG -> "hadoop202:9092,hadoop203:9092,hadoop204:9092",
      ConsumerConfig.GROUP_ID_CONFIG -> "bigdata"
    )

    //4.使用DirectAPI自动维护offset的方式读取Kafka数据创建DStream
    val kafkaDStream: InputDStream[(String, String)] = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc,
      kafkaParams,
      Set("mybak"))

    //5.计算WordCount并打印
    kafkaDStream.map(_._2)
      .flatMap(_.split(" "))
      .map((_, 1))
      .reduceByKey(_ + _)
      .print()

    //6.返回结果
    ssc
  }

```

编写代码（手动维护offset）
```scala
  def main(args: Array[String]): Unit = {

    //1.创建SparkConf
    val conf: SparkConf = new SparkConf().setAppName("DirectAPI_Handler").setMaster("local[*]")

    //2.创建StreamingContext
    val ssc = new StreamingContext(conf, Seconds(3))

    //3.创建Kafka参数
    val kafkaParams: Map[String, String] = Map[String, String](
      ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG -> "hadoop202:9092,hadoop203:9092,hadoop204:9092",
      ConsumerConfig.GROUP_ID_CONFIG -> "bigdata"
    )

    //4.获取上一次消费的位置信息
    val fromOffsets: Map[TopicAndPartition, Long] = Map[TopicAndPartition, Long](
      TopicAndPartition("mybak", 0) -> 13L,
      TopicAndPartition("mybak", 1) -> 10L
    )

    //5.使用DirectAPI手动维护offset的方式消费数据
    val kafakDStream: InputDStream[String] = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder, String](
      ssc,
      kafkaParams,
      fromOffsets,
      (m: MessageAndMetadata[String, String]) => m.message())

    //6.定义空集合用于存放数据的offset
    var offsetRanges = Array.empty[OffsetRange]

    //7.将当前消费到的offset进行保存
    kafakDStream.transform { rdd =>
      offsetRanges = rdd.asInstanceOf[HasOffsetRanges].offsetRanges
      rdd
    }.foreachRDD { rdd =>
      for (o <- offsetRanges) {
        println(s"${o.fromOffset}-${o.untilOffset}")
      }
    }

    //8.开启任务
    ssc.start()
    ssc.awaitTermination()

  }

```

## Kafka 0-10 Direct模式
需求：通过SparkStreaming从Kafka读取数据，并将读取过来的数据做简单计算，最终打印到控制台。

导入依赖(为了避免和0-8冲突，新建一个module)
```xml
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-core_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming_2.11</artifactId>
    <version>2.1.1</version>
</dependency>
<dependency>
    <groupId>org.apache.spark</groupId>
    <artifactId>spark-streaming-kafka-0-10_2.11</artifactId>
    <version>2.1.1</version>
</dependency>

```

编写代码
```scala
def main(args: Array[String]): Unit = {

    //1.创建SparkConf
    val conf: SparkConf = new SparkConf().setAppName("DirectAPI010").setMaster("local[*]")

    //2.创建StreamingContext
    val ssc = new StreamingContext(conf, Seconds(3))

    //3.构建Kafka参数
    val kafkaParmas: Map[String, Object] = Map[String, Object](
      ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG -> "hadoop102:9092,hadoop103:9092,hadoop104:9092",
      ConsumerConfig.GROUP_ID_CONFIG -> "bigdata191122",
      ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG -> "org.apache.kafka.common.serialization.StringDeserializer",
      ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG -> classOf[StringDeserializer]
    )

    //4.消费Kafka数据创建流
    val kafkaDStream: InputDStream[ConsumerRecord[String, String]] = KafkaUtils.createDirectStream[String, String](ssc,
      LocationStrategies.PreferConsistent,
      ConsumerStrategies.Subscribe[String, String](Set("test"), kafkaParmas))

    //5.计算WordCount并打印
    kafkaDStream.map(_.value())
      .flatMap(_.split(" "))
      .map((_, 1))
      .reduceByKey(_ + _)
      .print()

    //6.启动任务
    ssc.start()
    ssc.awaitTermination()

  }


```

## 消费Kafka数据模式总结

0-8 ReceiverAPI:
1. 专门的Executor读取数据，速度不统一
2. 跨机器传输数据，WAL
3. Executor读取数据通过多个线程的方式，想要增加并行度，则需要多个流union
4. offset存储在Zookeeper中


0-8 DirectAPI:
1. Executor读取数据并计算
2. 增加Executor个数来增加消费的并行度
3. offset存储
4. CheckPoint(getActiveOrCreate方式创建StreamingContext)
5. 手动维护(有事务的存储系统)
6. 获取offset必须在第一个调用的算子中：offsetRanges = rdd.asInstanceOf\[HasOffsetRanges\].offsetRanges


0-10 DirectAPI:
1. Executor读取数据并计算
2. 增加Executor个数来增加消费的并行度
3. offset存储
	1. \_\_consumer_offsets系统主题中
	2. 手动维护(有事务的存储系统)