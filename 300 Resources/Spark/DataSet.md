---
Create: 2022年 三月 17日, 星期四 22:54
tags: 
  - Engineering/spark
  - 大数据
---
DataSet是具有强类型的数据集合，需要提供对应的类型信息。

# 创建DataSet
## 使用样例类序列创建DataSet
```scala
scala> case class Person(name: String, age: Long)
defined class Person

scala> val caseClassDS = Seq(Person("wangyuyan",2)).toDS()

caseClassDS: org.apache.spark.sql.Dataset[Person] = [name: string, age: Long]

scala> caseClassDS.show
+---------+---+
|     name|age|
+---------+---+
|wangyuyan|  2|
+---------+---+
```

## 使用基本类型的序列创建DataSet
```scala
scala> val ds = Seq(1,2,3,4,5,6).toDS
ds: org.apache.spark.sql.Dataset[Int] = [value: int]

scala> ds.show
+-----+
|value|
+-----+
|    1|
|    2|
|    3|
|    4|
|    5|
|    6|
+-----+
```
> 注意:在实际使用的时候，很少用到把序列转换成DataSet，更多是通过RDD来得到DataSet
# RDD 转换为 DataSet
SparkSQL能够自动将包含有样例类的RDD转换成DataSet，样例类定义了table的结构，样例类属性通过反射变成了表的列名。样例类可以包含诸如Seq或者Array等复杂的结构。

1. 创建一个RDD
	```scala
	scala> val peopleRDD = sc.textFile("/opt/module/spark-local/people.txt")

	peopleRDD: org.apache.spark.rdd.RDD[String] = /opt/module/spark-local/people.txt MapPartitionsRDD[19] at textFile at <console>:24
	```
2. 创建一个样例类
	```scala
	scala> case class Person(name:String,age:Int)
	defined class Person
	```
3. 将RDD转化为DataSet
	```scala
	scala> peopleRDD.map(line => {val fields = line.split(",");Person(fields(0),fields(1). toInt)}).toDS

	res0: org.apache.spark.sql.Dataset[Person] = [name: string, age: Long]

	```


# DataSet 转换为 RDD
调用rdd方法即可

1. 创建一个DataSet

	```scala
	scala> val DS = Seq(Person("zhangcuishan", 32)).toDS()

	DS: org.apache.spark.sql.Dataset[Person] = [name: string, age: Long]
	```

2. 将DataSet转换为RDD
	```scala
	scala> DS.rdd

	res1: org.apache.spark.rdd.RDD[Person] = MapPartitionsRDD[6] at rdd at <console>:28
	```