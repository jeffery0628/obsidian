---
Create: 2022年 三月 15日, 星期二 13:15
tags: 
  - Engineering/spark
  - 大数据
---

# 简介
累加器：分布式共享只写变量。（==Task和Task之间不能读数据==）
累加器用来对信息进行聚合，通常在向Spark传递函数时，比如使用map()函数或者用 filter()传条件时，可以使用驱动器程序中定义的变量，但是集群中运行的每个任务都会得到这些变量的一份新的副本，更新这些副本的值也不会影响驱动器中的对应变量。如果我们想实现所有分片处理时更新共享变量的功能，那么累加器可以实现我们想要的效果。

# 系统累加器
```scala
  def main(args: Array[String]): Unit = {

    //1.创建SparkConf并设置App名称
    val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[1]")

    //2.创建SparkContext，该对象是提交Spark App的入口
    val sc: SparkContext = new SparkContext(conf)

    //3.创建RDD
    val dataRDD: RDD[(String, Int)] = sc.makeRDD(List(("a", 1), ("a", 2), ("a", 3), ("a", 4)),2)

    //3.1 打印单词出现的次数（a,10） 代码执行了shuffle
    dataRDD.reduceByKey(_ + _).collect().foreach(println)

    //3.2 如果不用shuffle，怎么处理呢？
    var sum = 0
    // 打印是在Executor端
    dataRDD.foreach {
      case (a, count) => {
        sum = sum + count
        println("sum=" + sum)
      }
    }
    // 打印是在Driver端
    println(("a", sum))

    //3.3 使用累加器实现数据的聚合功能
    // Spark自带常用的累加器
    //3.3.1 声明累加器
    val sum1: LongAccumulator = sc.longAccumulator("sum1")

    dataRDD.foreach{
      case (a, count)=>{
        //3.3.2 使用累加器
        sum1.add(count)
      }
    }

    //3.3.3 获取累加器
    println(sum1.value)

    //4.关闭连接
    sc.stop()
  }
```

通过在驱动器中调用SparkContext.accumulator(initialValue)方法，创建出存有初始值的累加器。返回值为org.apache.spark.Accumulator[T]对象，其中T是初始值initialValue的类型。Spark闭包里的执行器代码可以使用累加器的+=方法(在Java中是 add)增加累加器的值。驱动器程序可以调用累加器的value属性(在Java中使用value()或setValue())来访问累加器的值。
> 注意：
> 1. 工作节点上的任务不能相互访问累加器的值。从这些任务的角度来看，累加器是一个只写变量。
> 2. 对于要在行动操作中使用的累加器，Spark只会把每个任务对各累加器的修改应用一次。因此，如果想要一个无论在失败还是重复计算时都绝对可靠的累加器，我们必须把它放在foreach()这样的行动操作中。转化操作中累加器可能会发生不止一次更新。

# 自定义累加器
自定义累加器类型的功能在1.X版本中就已经提供了，但是使用起来比较麻烦，在2.0版本后，累加器的易用性有了较大的改进，而且官方还提供了一个新的抽象类：AccumulatorV2来提供更加友好的自定义类型累加器的实现方式。
## 自定义累加器步骤
1. 继承AccumulatorV2，设定输入、输出泛型
2. 重写方法


需求描述：自定义累加器，统计集合中首字母为“H”单词出现的次数。
List("Hello", "Hello", "Hello", "Hello", "Hello", "Spark", "Spark")

```scala
def main(args: Array[String]): Unit = {  
  
  //1.创建SparkConf并设置App名称  
 val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")  
  //2.创建SparkContext，该对象是提交Spark App的入口  
 val sc: SparkContext = new SparkContext(conf)  
  //3. 创建RDD  
 val rdd: RDD[String] = sc.makeRDD(List("Hello", "Hello", "Hello", "Hello", "Hello", "Spark", "Spark"))  
  //3.1 创建累加器  
 val accumulator1: MyAccumulator = new MyAccumulator()  
  //3.2 注册累加器  
 sc.register(accumulator1,"wordcount")  
  //3.3 使用累加器  
 rdd.foreach(  
    word =>{  
      accumulator1.add(word)  
    }  
  )  
  //3.4 获取累加器的累加结果  
 println(accumulator1.value)  
  //4.关闭连接  
 sc.stop()  
}


// 声明累加器  
// 1.继承AccumulatorV2,设定输入、输出泛型  
// 2.重新方法  
class MyAccumulator extends AccumulatorV2[String, mutable.Map[String, Long]] {  
  // 定义输出数据集合  
 var map = mutable.Map[String, Long]()  
  
  // 是否为初始化状态，如果集合数据为空，即为初始化状态  
 override def isZero: Boolean = map.isEmpty  
  // 复制累加器  
 override def copy(): AccumulatorV2[String, mutable.Map[String, Long]] = {  
    new MyAccumulator()  
  }  
  
  // 重置累加器  
 override def reset(): Unit = map.clear()  
  // 增加数据  
 override def add(v: String): Unit = {  
    // 业务逻辑  
 if (v.startsWith("H")) {  
      map(v) = map.getOrElse(v, 0L) + 1L  
 }  
  }  
  
  // 合并累加器  
 override def merge(other: AccumulatorV2[String, mutable.Map[String, Long]]): Unit = {  
  
    var map1 = map  
 var map2 = other.value  
  
    map = map1.foldLeft(map2)(  
      (map,kv)=>{  
        map(kv._1) = map.getOrElse(kv._1, 0L) + kv._2  
        map  
      }  
    )  
  }  
  
  // 累加器的值，其实就是累加器的返回结果  
 override def value: mutable.Map[String, Long] = map  
}
```