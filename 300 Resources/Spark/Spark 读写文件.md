---
Create: 2021年 十二月 1日, 星期三 12:59
tags: 
  - Engineering/spark
  - 大数据
---
从文件中读取数据是创建 RDD 的一种方式。把数据保存的文件中的操作是一种 Action。

Spark 的数据读取及数据保存可以从两个维度来作区分：

- 文件格式
	- Text文件
	- Json文件
	- csv文件
	- Sequence文件
	- Object文件
- 文件系统
	- 本地文件系统
	- HDFS
	- Hbase
	- 数据库

平时用的比较多的就是: 从 HDFS 读取和保存 Text 文件.



# 读写 txt 文件

```scala
// 读取
val rdd1 = sc.textFile("./words.txt")

// 保存
val rdd2 = rdd1.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_ +_)
rdd2.saveAsTextFile("hdfs://hadoop201:9000/words_output")
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-file-read-save/image-20210920202118264.png)

# 读写 json 文件

如果 JSON 文件中每一行就是一个 JSON 记录，那么可以通过将 JSON 文件当做文本文件来读取，然后利用相关的 JSON 库对每一条数据进行 JSON 解析。

> 注意：使用 RDD 读取 JSON 文件处理很复杂，同时 SparkSQL 集成了很好的处理 JSON 文件的方式，所以实际应用中多是采用SparkSQL处理JSON文件。

```scala
// 读取 json 数据的文件, 每行是一个 json 对象
val rdd1 = sc.textFile("/opt/module/spark-local/examples/src/main/resources/people.json")

// 使用 map 来解析 Json, 需要传入 JSON.parseFull
import scala.util.parsing.json.JSON
val rdd2 = rdd1.map(JSON.parseFull)

```



# 读写 SequenceFile  文件

 SequenceFile 文件是 Hadoop 用来存储二进制形式的 key-value 对而设计的一种平面文件(Flat File)。

Spark 有专门用来读取 `SequenceFile `的接口。在 SparkContext 中，可以调用 `sequenceFile[ keyClass, valueClass](path)`。

> 注意：SequenceFile 文件只针对 PairRDD



```scala
// 保存
val rdd1 = sc.parallelize(Array(("a", 1),("b", 2),("c", 3)))
rdd1.saveAsSequenceFile("hdfs://hadoop201:9000/seqFiles")

// 读取
val rdd1 = sc.sequenceFile[String, Int]("hdfs://hadoop201:9000/seqFiles")
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-file-read-save/image-20210920202100133.png)



# 读写 objectFile 文件

对象文件是将对象序列化后保存的文件，采用 Java 的序列化机制。

可以通过`objectFile[k,v](path) `函数接收一个路径，读取对象文件，返回对应的 RDD，也可以通过调用`saveAsObjectFile()`实现对对象文件的输出。

```scala
// 保存
val rdd1 = sc.parallelize(Array(("a", 1),("b", 2),("c", 3)))
rdd1.saveAsObjectFile("hdfs://hadoop201:9000/obj_file")

// 读取
val rdd1 = sc.objectFile[(String, Int)]("hdfs://hadoop201:9000/obj_file")
```



# 从 HDFS 读写文件

Spark 的整个生态系统与 Hadoop 完全兼容的,所以对于 Hadoop 所支持的文件类型或者数据库类型,Spark 也同样支持。

另外,由于 Hadoop 的 API 有新旧两个版本,所以 Spark 为了能够兼容 Hadoop 所有的版本,也提供了两套创建操作接口。

对于外部存储创建操作而言,HadoopRDD 和 newHadoopRDD 是最为抽象的两个函数接口,主要包含以下四个参数：

1. 输入格式(InputFormat): 制定数据输入的类型,如 TextInputFormat 等,新旧两个版本所引用的版本分别是 org.apache.hadoop.mapred.InputFormat 和org.apache.hadoop.mapreduce.InputFormat(NewInputFormat)
2. 键类型: 指定[K,V]键值对中K的类型
3. 值类型: 指定[K,V]键值对中V的类型
4. 分区值: 指定由外部存储生成的RDD的partition数量的最小值,如果没有指定,系统会使用默认值defaultMinSplits

> 注意:其他创建操作的API接口都是为了方便最终的Spark程序开发者而设置的,是这两个接口的高效实现版本.例如,对于textFile而言,只有path这个指定文件路径的参数,其他参数在系统内部指定了默认值。
>
> 1. 在Hadoop中以压缩形式存储的数据,不需要指定解压方式就能够进行读取,因为Hadoop本身有一个解压器会根据压缩文件的后缀推断解压算法进行解压.
> 2.  如果用Spark从Hadoop中读取某种类型的数据不知道怎么读取的时候,上网查找一个使用map-reduce的时候是怎么读取这种这种数据的,然后再将对应的读取方式改写成上面的hadoopRDD和newAPIHadoopRDD两个类就行了



# 从 Mysql 数据读写文件

依赖：

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.27</version>
</dependency>
```



## 从mysql 中读取

```scala
val driver = "com.mysql.jdbc.Driver"
val url = "jdbc:mysql://hadoop201:3306/rdd"
val userName = "root"
val passWd = "aaa"

val rdd = new JdbcRDD(
    sc,
    () => {
        Class.forName(driver)
        DriverManager.getConnection(url, userName, passWd)
    },
    "select id, name from user where id >= ? and id <= ?",
    1,
    20,
    2,
    result => (result.getInt(1), result.getString(2))
	)
rdd.collect.foreach(println)
```



## 向mysql 中写入

```scala
//定义连接mysql的参数
val driver = "com.mysql.jdbc.Driver"
val url = "jdbc:mysql://hadoop201:3306/rdd"
val userName = "root"
val passWd = "aaa"

val rdd: RDD[(Int, String)] = sc.parallelize(Array((110, "police"), (119, "fire")))
// 对每个分区执行 参数函数
rdd.foreachPartition(it => {
    Class.forName(driver)
    val conn: Connection = DriverManager.getConnection(url, userName, passWd)
    it.foreach(x => {
        val statement: PreparedStatement = conn.prepareStatement("insert into user values(?, ?)")
        statement.setInt(1, x._1)
        statement.setString(2, x._2)
        statement.executeUpdate()
    })
})
```



# 从 Hbase 读写文件

由于 org.apache.hadoop.hbase.mapreduce.TableInputFormat 类的实现，Spark 可以通过Hadoop输入格式访问 HBase。

这个输入格式会返回键值对数据，其中键的类型为org. apache.hadoop.hbase.io.ImmutableBytesWritable，而值的类型为org.apache.hadoop.hbase.client.Result。

依赖：

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-server</artifactId>
    <version>1.3.1</version>
    <exclusions>
        <exclusion>
            <groupId>org.mortbay.jetty</groupId>
            <artifactId>servlet-api-2.5</artifactId>
        </exclusion>
        <exclusion>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
        </exclusion>
    </exclusions>

</dependency>

<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-client</artifactId>
    <version>1.3.1</version>
    <exclusions>
        <exclusion>
            <groupId>org.mortbay.jetty</groupId>
            <artifactId>servlet-api-2.5</artifactId>
        </exclusion>
        <exclusion>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## 从 HBase 读取数据

```scala
val hbaseConf: Configuration = HBaseConfiguration.create()

hbaseConf.set("hbase.zookeeper.quorum", "hadoop201,hadoop202,hadoop203")
hbaseConf.set(TableInputFormat.INPUT_TABLE, "student")

val rdd: RDD[(ImmutableBytesWritable, Result)] = sc.newAPIHadoopRDD(
    hbaseConf,
    classOf[TableInputFormat],
    classOf[ImmutableBytesWritable],
    classOf[Result])

val rdd2: RDD[String] = rdd.map {
    case (_, result) => Bytes.toString(result.getRow)
}
rdd2.collect.foreach(println)
```



## 向 HBase 写入数据

```scala
val hbaseConf = HBaseConfiguration.create()
hbaseConf.set("hbase.zookeeper.quorum", "hadoop201,hadoop202,hadoop203")
hbaseConf.set(TableOutputFormat.OUTPUT_TABLE, "student")
// 通过job来设置输出的格式的类
val job = Job.getInstance(hbaseConf)
job.setOutputFormatClass(classOf[TableOutputFormat[ImmutableBytesWritable]])
job.setOutputKeyClass(classOf[ImmutableBytesWritable])
job.setOutputValueClass(classOf[Put])

val initialRDD = sc.parallelize(List(("100", "apple", "11"), ("200", "banana", "12"), ("300", "pear", "13")))
val hbaseRDD = initialRDD.map(x => {
    val put = new Put(Bytes.toBytes(x._1))
    put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes(x._2))
    put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("weight"), Bytes.toBytes(x._3))
    (new ImmutableBytesWritable(), put)
})
hbaseRDD.saveAsNewAPIHadoopDataset(job.getConfiguration)
```






