---
Create: 2022年 三月 18日, 星期五 10:29
tags: 
  - Engineering/spark
  - 大数据
---
 添加依赖

```xml
<dependency>
<groupId>org.apache.spark</groupId>
<artifactId>spark-sql_2.11</artifactId>
<version>2.1.1</version>
</dependency>

```




代码

```scala
  def main(args: Array[String]): Unit = {
    //创建上下文环境配置对象
    val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSQL01_Demo")

    //创建SparkSession对象
    val spark: SparkSession = SparkSession.builder().config(conf).getOrCreate()
    //RDD=>DataFrame=>DataSet转换需要引入隐式转换规则，否则无法转换
    //spark不是包名，是上下文环境对象名
    import spark.implicits._

    //读取json文件 创建DataFrame  {"username": "lisi","age": 18}
    val df: DataFrame = spark.read.json("D:\\dev\\workspace\\spark-bak\\spark-bak-00\\input\\test.json")
    //df.show()

    //SQL风格语法
    df.createOrReplaceTempView("user")
    //spark.sql("select avg(age) from user").show

    //DSL风格语法
    //df.select("username","age").show()

    //*****RDD=>DataFrame=>DataSet*****
    //RDD
    val rdd1: RDD[(Int, String, Int)] = spark.sparkContext.makeRDD(List((1,"qiaofeng",30),(2,"xuzhu",28),(3,"duanyu",20)))

    //DataFrame
    val df1: DataFrame = rdd1.toDF("id","name","age")
    //df1.show()

    //DateSet
    val ds1: Dataset[User] = df1.as[User]
    //ds1.show()

    //*****DataSet=>DataFrame=>RDD*****
    //DataFrame
    val df2: DataFrame = ds1.toDF()

    //RDD  返回的RDD类型为Row，里面提供的getXXX方法可以获取字段值，类似jdbc处理结果集，但是索引从0开始
    val rdd2: RDD[Row] = df2.rdd
    //rdd2.foreach(a=>println(a.getString(1)))

    //*****RDD=>DataSe*****
    rdd1.map{
      case (id,name,age)=>User(id,name,age)
    }.toDS()

    //*****DataSet=>=>RDD*****
    ds1.rdd

    //释放资源
    spark.stop()
  }

case class User(id:Int,name:String,age:Int)

```

