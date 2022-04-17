---
Create: 2022年 三月 18日, 星期五 10:31
tags: 
  - 

---

# UDF
输入一行，返回一个结果。在Shell窗口中可以通过spark.udf功能用户可以自定义函数。
1. 创建DataFrame

	```scala
	scala> val df = spark.read.json("/opt/module/spark-local/people.json")
	df: org.apache.spark.sql.DataFrame = [age: bigint， name: string]
	```
2. 打印数据

	```scala
	scala> df.show
	+---+--------+
	|age|    name|
	+---+--------+
	| 18|qiaofeng|
	| 19|  duanyu|
	| 20|   xuzhu|
	+---+--------+
	```
	
3. 注册UDF，功能为在数据前添加字符串
	```scala
	scala> spark.udf.register("addName",(x:String)=> "Name:"+x)
	res9: org.apache.spark.sql.expressions.UserDefinedFunction = UserDefinedFunction(<function1>,StringType,Some(List(StringType)))
	```
4. 创建临时表

	```scala
	scala> df.createOrReplaceTempView("people")
	```
5. 应用UDF
```scala
scala> spark.sql("Select addName(name),age from people").show()
+-----------------+---+
|UDF:addName(name)|age|
+-----------------+---+
|    Name:qiaofeng| 18|
|      Name:duanyu| 19|
|       Name:xuzhu| 20|
+-----------------+---+
```

# UDAF
输入多行,返回一行。强类型的Dataset和弱类型的DataFrame都提供了相关的聚合函数， 如 count()，countDistinct()，avg()，max()，min()。除此之外，用户可以设定自己的自定义聚合函数。通过继承UserDefinedAggregateFunction来实现用户自定义聚合函数。

需求：实现求平均年龄
## RDD算子方式实现
```scala
  def main(args: Array[String]): Unit = {
    //1.创建SparkConf并设置App名称
    val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
    //2.创建SparkContext，该对象是提交Spark App的入口
    val sc: SparkContext = new SparkContext(conf)
    val res: (Int, Int) = sc.makeRDD(List(("zhangsan", 20), ("lisi", 30), ("wangw", 40))).map {
      case (name, age) => {
        (age, 1)
      }
    }.reduce {
      (t1, t2) => {
        (t1._1 + t2._1, t1._2 + t2._2)
      }
    }
    println(res._1/res._2)
    // 关闭连接
    sc.stop()
  }
```
## 自定义累加器实现
自定义累加器方式实现，减少shuffle，提高效率（模仿LongAccumulator）
```scala
  def main(args: Array[String]): Unit = {
    //1.创建SparkConf并设置App名称
    val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

    //2.创建SparkContext，该对象是提交Spark App的入口
    val sc: SparkContext = new SparkContext(conf)

    var sumAc = new MyAC
    sc.register(sumAc)
    sc.makeRDD(List(("zhangsan",20),("lisi",30),("wangw",40))).foreach{
      case (name,age)=>{
        sumAc.add(age)
      }
    }
    println(sumAc.value)

    // 关闭连接
    sc.stop()
  }

  
class MyAC extends AccumulatorV2[Int,Int]{  
  var sum:Int = 0  
 var count:Int = 0  
 override def isZero: Boolean = {  
    return sum ==0 && count == 0  
 }  
  
  override def copy(): AccumulatorV2[Int, Int] = {  
    val newMyAc = new MyAC  
    newMyAc.sum = this.sum  
 newMyAc.count = this.count  
 newMyAc  
  }  
  
  override def reset(): Unit = {  
    sum =0  
 count = 0  
 }  
  
  override def add(v: Int): Unit = {  
    sum += v  
    count += 1  
 }  
  
  override def merge(other: AccumulatorV2[Int, Int]): Unit = {  
    other match {  
      case o:MyAC=>{  
        sum += o.sum  
 count += o.count  
 }  
      case _=>  
    }  
  
  }  
  
  override def value: Int = sum/count  
}


```

## 自定义聚合函数实现-弱类型
应用于SparkSql更方便
```scala
def main(args: Array[String]): Unit = {  
  //创建上下文环境配置对象  
 val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSQL01_Demo")  
  //创建SparkSession对象  
 val spark: SparkSession = SparkSession.builder().config(conf).getOrCreate()  
  import spark.implicits._  
  
  //创建聚合函数  
 var myAverage = new MyAveragUDAF  
  
  //在spark中注册聚合函数  
 spark.udf.register("avgAge",myAverage)  
  
  //读取数据  {"username": "zhangsan","age": 20}  
 val df: DataFrame = spark.read.json("D:\\dev\\workspace\\spark-bak\\spark-bak-00\\input\\test.json")  
  
  //创建临时视图  
 df.createOrReplaceTempView("user")  
  
  //使用自定义函数查询  
 spark.sql("select avgAge(age) from user").show()  
}

/*  
定义类继承UserDefinedAggregateFunction，并重写其中方法  
*/  
class MyAveragUDAF extends UserDefinedAggregateFunction {  
  
  // 聚合函数输入参数的数据类型  
 def inputSchema: StructType = StructType(Array(StructField("age",IntegerType)))  
  
  // 聚合函数缓冲区中值的数据类型(age,count)  
 def bufferSchema: StructType = {  
    StructType(Array(StructField("sum",LongType),StructField("count",LongType)))  
  }  
  
  // 函数返回值的数据类型  
 def dataType: DataType = DoubleType  
  
  // 稳定性：对于相同的输入是否一直返回相同的输出。  
 def deterministic: Boolean = true  
  
 // 函数缓冲区初始化  
 def initialize(buffer: MutableAggregationBuffer): Unit = {  
    // 存年龄的总和  
 buffer(0) = 0L  
 // 存年龄的个数  
 buffer(1) = 0L  
 }  
  
  // 更新缓冲区中的数据  
 def update(buffer: MutableAggregationBuffer,input: Row): Unit = {  
    if (!input.isNullAt(0)) {  
      buffer(0) = buffer.getLong(0) + input.getInt(0)  
      buffer(1) = buffer.getLong(1) + 1  
 }  
  }  
  
  // 合并缓冲区  
 def merge(buffer1: MutableAggregationBuffer,buffer2: Row): Unit = {  
    buffer1(0) = buffer1.getLong(0) + buffer2.getLong(0)  
    buffer1(1) = buffer1.getLong(1) + buffer2.getLong(1)  
  }  
  
  // 计算最终结果  
 def evaluate(buffer: Row): Double = buffer.getLong(0).toDouble / buffer.getLong(1)  
}
```
## 自定义聚合函数实现-强类型
应用于DataSet的DSL更方便
```scala
def main(args: Array[String]): Unit = {  
  //创建上下文环境配置对象  
 val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkSQL01_Demo")  
  //创建SparkSession对象  
 val spark: SparkSession = SparkSession.builder().config(conf).getOrCreate()  
  import spark.implicits._  
  
  //读取数据  {"username": "zhangsan","age": 20}  
 val df: DataFrame = spark.read.json("D:\\dev\\workspace\\spark-bak\\spark-bak-00\\input\\test.json")  
  
  //封装为DataSet  
 val ds: Dataset[User01] = df.as[User01]  
  
  //创建聚合函数  
 var myAgeUdtf1 = new MyAveragUDAF1  
  //将聚合函数转换为查询的列  
 val col: TypedColumn[User01, Double] = myAgeUdtf1.toColumn  
  
  //查询  
 ds.select(col).show()  
  
  // 关闭连接  
 spark.stop()  
}


  
//输入数据类型  
case class User01(username:String,age:Long)  
//缓存类型  
case class AgeBuffer(var sum:Long,var count:Long)  
/**  
 * 定义类继承org.apache.spark.sql.expressions.Aggregator * 重写类中的方法 */class MyAveragUDAF1 extends Aggregator[User01,AgeBuffer,Double]{  
  override def zero: AgeBuffer = {  
    AgeBuffer(0L,0L)  
  }  
  
  override def reduce(b: AgeBuffer, a: User01): AgeBuffer = {  
    b.sum = b.sum + a.age  
    b.count = b.count + 1  
 b  
  }  
  
  override def merge(b1: AgeBuffer, b2: AgeBuffer): AgeBuffer = {  
    b1.sum = b1.sum + b2.sum  
    b1.count = b1.count + b2.count  
    b1  
  }  
  
  override def finish(buff: AgeBuffer): Double = {  
    buff.sum.toDouble/buff.count  
  }  
  //DataSet默认额编解码器，用于序列化，固定写法  
 //自定义类型就是produce   自带类型根据类型选择 override def bufferEncoder: Encoder[AgeBuffer] = {  
    Encoders.product  
  }  
  
  override def outputEncoder: Encoder[Double] = {  
    Encoders.scalaDouble  
  }  
}  
  
输出结果：  
+--------------------------------------------------+  
|MyAveragUDAF1(com.atguigu.spark.core.day05.User01)|  
  +--------------------------------------------------+  
|                                              18.0|  
  +--------------------------------------------------+

```

# UDTF
输入一行，返回多行(hive)；
SparkSQL中没有UDTF，spark中用flatMap即可实现该功能


