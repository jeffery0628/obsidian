---
Create: 2021年 十二月 1日, 星期三 10:01
tags: 
  - Engineering/spark
  - 大数据
---

# 引入

进行 Spark 进行编程的时候, 初始化工作是在 **driver**端完成的, 而实际的运行程序是在**executor**端进行的. 所以就涉及到了进程间的通讯, 数据是需要序列化的。
![[700 Attachments/Pasted image 20220314102619.png]]

# 闭包
## 闭包引入
```scala
  def main(args: Array[String]): Unit = {

    //1.创建SparkConf并设置App名称
    val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")

    //2.创建SparkContext，该对象是提交Spark App的入口
    val sc: SparkContext = new SparkContext(conf)

    //3.创建两个对象
    val user1 = new User()
    user1.name = "zhangsan"

    val user2 = new User()
    user2.name = "lisi"

    val userRDD1: RDD[User] = sc.makeRDD(List(user1, user2))

    //3.1 打印，ERROR报java.io.NotSerializableException
    userRDD1.foreach(user => println(user.name))


    //3.2 打印，RIGHT
    val userRDD2: RDD[User] = sc.makeRDD(List())
    //userRDD2.foreach(user => println(user.name))

    //3.3 打印，ERROR Task not serializable 注意：没执行就报错了
    userRDD2.foreach(user => println(user.name))

    //4.关闭连接
    sc.stop()
  }


//class User {  
//    var name: String = _  
//}  
class User extends Serializable {  
  var name: String = _  
}
```

## 闭包检查
![[700 Attachments/Pasted image 20220314132018.png]]





需求: 在 RDD 中查找出来包含 query 子字符串的元素

```scala
// query 为需要查找的子字符串
class Searcher(val query: String){
  // 判断 s 中是否包括子字符串 query
  def isMatch(s : String) ={
    s.contains(query)
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD1(rdd: RDD[String]) ={
    rdd.filter(isMatch)  //
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD2(rdd: RDD[String]) ={
    rdd.filter(_.contains(query))
  }
}

def main(args: Array[String]): Unit = {

  val spark = SparkSession.builder()
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()

  val sc = spark.sparkContext
  val rdd1 = sc.parallelize(Array("hello world", "hello world","hello jeffery", "jeffery", "hahah"), 2)
  val searcher = new Searcher("hello")
  val result: RDD[String] = searcher.getMatchedRDD1(rdd1)
  result.collect.foreach(println)
  sc.stop()
}

```

> 直接运行程序会报错: 没有初始化. 因为**rdd.filter(isMatch)** 用到了对象**this**的方法**isMatch**, 所以对象**this**需要序列化,才能把对象从**driver**发送到**executor**.
>
> ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-rdd-programing/image-20210920135709389.png)
>
> 解决方案: 让 **Searcher** 类实现序列化接口:**Serializable**

```scala
// query 为需要查找的子字符串
class Searcher(val query: String)  extends Serializable {
  // 判断 s 中是否包括子字符串 query
  def isMatch(s : String) ={
    s.contains(query)
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD1(rdd: RDD[String]) ={
    rdd.filter(isMatch)  //
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD2(rdd: RDD[String]) ={
    rdd.filter(_.contains(query))
  }
}

def main(args: Array[String]): Unit = {

  val spark = SparkSession.builder()
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()

  val sc = spark.sparkContext
  val rdd1 = sc.parallelize(Array("hello world", "hello world","hello jeffery", "jeffery", "hahah"), 2)
  val searcher = new Searcher("hello")
  val result: RDD[String] = searcher.getMatchedRDD1(rdd1)
  result.collect.foreach(println) // hello world  hello world  hello jeffery
  sc.stop()
}
```



# 传递变量
说明：
Driver：算子以外的代码都是在Driver端执行
Executor：算子里面的代码都是在Executor端执行

```scala
// query 为需要查找的子字符串
class Searcher(val query: String)  extends Serializable {
  // 判断 s 中是否包括子字符串 query
  def isMatch(s : String) ={
    s.contains(query)
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD1(rdd: RDD[String]) ={
    rdd.filter(isMatch)  // 传递的是成员函数
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD2(rdd: RDD[String]) ={
    rdd.filter(_.contains(query))   // 传递的是成员变量
  }
}

def main(args: Array[String]): Unit = {

  val spark = SparkSession.builder()
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()

  val sc = spark.sparkContext
  val rdd1 = sc.parallelize(Array("hello world", "hello world","hello jeffery", "jeffery", "hahah"), 2)
  val searcher = new Searcher("hello")
  val result: RDD[String] = searcher.getMatchedRDD2(rdd1)   // 这里发生了变化，getMatchedRDD2使用了 成员变量 query
  result.collect.foreach(println) // hello world  hello world  hello jeffery
  sc.stop()
}
```

> 这次没有传递函数, 而是传递了一个属性过去. 仍然会报错没有序列化. 因为**this**仍然没有序列化，解决方案有 2 种:
>
> 1. 让类实现序列化接口:**Serializable**
>
> 2. 传递局部变量而不是属性.
>
> 	```scala
> 	def getMatchedRDD2(rdd: RDD[String]) ={
> 	    val q = query
> 	    rdd.filter(_.contains(q))   // 传递的是成员变量
> 	}
> 	```



# kyro 序列化框架

Java 的序列化比较重, 能够序列化任何的类，比较灵活，但是相当的慢。并且序列化后对象的体积也比较大。

Spark 出于性能的考虑, 支持另外一种序列化机制: kryo (2.0开始支持)。 kryo 比较快和简洁.(速度是**Serializable**的10倍). 想获取更好的性能应该使用 kryo 来序列化。

从2.0开始, Spark 内部已经在使用 kryo 序列化机制: 当 RDD 在 **Shuffle**数据的时候, 简单数据类型，简单数据类型的数组和字符串类型已经在使用 kryo 来序列化。有一点需要注意的是: 即使使用 kryo 序列化, 也要继承 **Serializable** 接口.

```scala
// query 为需要查找的子字符串
class Searcher(val query: String)  extends Serializable {
  // 判断 s 中是否包括子字符串 query
  def isMatch(s : String) ={
    s.contains(query)
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD1(rdd: RDD[String]) ={
    rdd.filter(isMatch)  // 传递的是成员函数
  }
  // 过滤出包含 query字符串的字符串组成的新的 RDD
  def getMatchedRDD2(rdd: RDD[String]) ={
    rdd.filter(_.contains(query))   // 传递的是成员变量
  }
}



def main(args: Array[String]): Unit = {

  val spark = SparkSession.builder()
    .config("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
    // use this if you need to increment Kryo buffer size. Default 64k
    .config("spark.kryoserializer.buffer", "1024k")
    // use this if you need to increment Kryo buffer max size. Default 64m
    .config("spark.kryoserializer.buffer.max", "1024m")
    /*
    * Use this if you need to register all Kryo required classes.
    * If it is false, you do not need register any class for Kryo, but it will increase your data size when the data is serializing.
    */
    .config("spark.kryo.registrationRequired", "true")
    .master("local[3]")
    .enableHiveSupport()
    .getOrCreate()

  val sc = spark.sparkContext
  val rdd1 = sc.parallelize(Array("hello world", "hello world","hello jeffery", "jeffery", "hahah"), 2)
  val searcher = new Searcher("hello")
  val result: RDD[String] = searcher.getMatchedRDD2(rdd1)
  result.collect.foreach(println)
  sc.stop()
}


```

> 只需在创建session时配置kryo






