---
Create: 2022年 三月 18日, 星期五 10:22
tags: 
  - Engineering/spark
  - 大数据
---

# 概述
在SparkSQL中Spark为我们提供了两个新的抽象，分别是DataFrame和DataSet。他们和RDD有什么区别呢？

首先从版本的产生上来看：
RDD (Spark1.0) —> Dataframe(Spark1.3) —> Dataset(Spark1.6)

如果同样的数据都给到这三个数据结构，他们分别计算之后，都会给出相同的结果。不同是的他们的执行效率和执行方式。在后期的Spark版本中，DataSet有可能会逐步取代RDD和DataFrame成为唯一的API接口。

# 三者共性
1. RDD、DataFrame、DataSet全都是spark平台下的分布式弹性数据集，为处理超大型数据提供便利;
2. 三者都有惰性机制，在进行创建、转换，如map方法时，不会立即执行，只有在遇到Action如foreach时，三者才会开始遍历运算;
3. 三者有许多共同的函数，如filter，排序等;
4. 在对DataFrame和Dataset进行操作许多操作都需要这个包:import spark.implicits.\_（在创建好SparkSession对象后尽量直接导入）
5. 三者都会根据 Spark 的内存情况自动缓存运算，这样即使数据量很大，也不用担心会内存溢出
6. 三者都有partition的概念
7. DataFrame和Dataset均可使用模式匹配获取各个字段的值和类型

# 三者的区别
## RDD								
- RDD一般和Spark MLib同时使用
- RDD不支SparkSQL操作
## DataFrame
- 与RDD和Dataset不同，DataFrame每一行的类型固定为Row，每一列的值没法直接访问，只有通过解析才能获取各个字段的值
- DataFrame与DataSet一般不与 Spark MLib 同时使用
- DataFrame与DataSet均支持 SparkSQL 的操作，比如select，groupby之类，还能注册临时表/视窗，进行 sql 语句操作
- DataFrame与DataSet支持一些特别方便的保存方式，比如保存成csv，可以带上表头，这样每一列的字段名一目了然(后面专门讲解)
## DataSet
- Dataset和DataFrame拥有完全相同的成员函数，区别只是每一行的数据类型不同。 DataFrame其实就是DataSet的一个特例  
type DataFrame = Dataset\[Row\]
- DataFrame也可以叫Dataset\[Row\],每一行的类型是Row，不解析，每一行究竟有哪些字段，各个字段又是什么类型都无从得知，只能用上面提到的getAS方法或者共性中的第七条提到的模式匹配拿出特定字段。而Dataset中，每一行是什么类型是不一定的，在自定义了case class之后可以很自由的获得每一行的信息


## 三者的互相转化
![[700 Attachments/Pasted image 20220318102809.png]]




