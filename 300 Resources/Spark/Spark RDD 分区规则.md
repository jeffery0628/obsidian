---
Create: 2022年 三月 11日, 星期五 13:20
tags: 
  - Engineering/spark
  - 大数据
---

# 分区源码
![[700 Attachments/Pasted image 20220311132431.png]]

## 代码验证
```scala
object partition01_default {

    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkCoreTest1")
        val sc: SparkContext = new SparkContext(conf)

        val rdd: RDD[Int] = sc.makeRDD(Array(1,2,3,4))

        rdd.saveAsTextFile("output")
    }
}

```
### 从集合创建RDD

```scala
object partition02_Array {

    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkCoreTest1")
        val sc: SparkContext = new SparkContext(conf)

        //1）4个数据，设置4个分区，输出：0分区->1，1分区->2，2分区->3，3分区->4
        //val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4), 4)

        //2）4个数据，设置3个分区，输出：0分区->1，1分区->2，2分区->3,4
        //val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4), 3)

        //3）5个数据，设置3个分区，输出：0分区->1，1分区->2、3，2分区->4、5
        val rdd: RDD[Int] = sc.makeRDD(Array(1, 2, 3, 4, 5), 3)

        rdd.saveAsTextFile("output")

        sc.stop()
    }
}

```

![[700 Attachments/Pasted image 20220311133358.png]]

### 从文件读取后创建RDD
```scala
object partition03_file {

    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setMaster("local[*]").setAppName("SparkCoreTest1")
        val sc: SparkContext = new SparkContext(conf)

        //1）默认分区的数量：默认取值为当前核数和2的最小值
        //val rdd: RDD[String] = sc.textFile("input")

        //2）输入数据1-4，每行一个数字；输出：0=>{1、2} 1=>{3} 2=>{4} 3=>{空}
        //val rdd: RDD[String] = sc.textFile("input/3.txt",3)

        //3）输入数据1-4，一共一行；输出：0=>{1234} 1=>{空} 2=>{空} 3=>{空} 
        val rdd: RDD[String] = sc.textFile("input/4.txt",3)

        rdd.saveAsTextFile("output")

        sc.stop()
    }
}
```

![[700 Attachments/Pasted image 20220311133728.png]]
> 注意：getSplits文件返回的是切片规划，真正读取是在compute方法中创建LineRecordReader读取的，有两个关键变量start=split.getStart()	   end = start + split.getLength

