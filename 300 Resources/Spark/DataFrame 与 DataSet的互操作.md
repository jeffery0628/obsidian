---
Create: 2022年 三月 17日, 星期四 23:05
tags: 
  - Engineering/spark
  - 大数据
---

# DataFrame转为DataSet

1. 创建一个DataFrame

	```scala
	scala> val df = spark.read.json("/opt/module/spark-local/people.json")

	df: org.apache.spark.sql.DataFrame = [age: bigint, name: string]
	```
2. 创建一个样例类

	```scala
	scala> case class Person(name: String,age: Long)
	defined class Person
	```

3. 将DataFrame转化为DataSet   
	```scala
	scala> df.as[Person]

	res5: org.apache.spark.sql.Dataset[Person] = [age: bigint, name: string]
	```

这种方法就是在给出每一列的类型后，使用as方法，转成Dataset，这在数据类型是DataFrame又需要针对各个字段处理时极为方便。在使用一些特殊的操作时，一定要加上 import spark.implicits._ 不然toDF、toDS无法使用。

# DataSet 转 DataFrame
1. 创建一个样例类

	```scala
	scala> case class Person(name: String,age: Long)
	defined class Person
	```

2.  创建DataSet

	```scala
	scala> val ds = Seq(Person("zhangwuji",32)).toDS()

	ds: org.apache.spark.sql.Dataset[Person] = [name: string, age: bigint]
	```

3. 将DataSet转化为DataFrame
```scala
scala> var df = ds.toDF
df: org.apache.spark.sql.DataFrame = [name: string, age: bigint]
```
4. 展示
	```scala
	scala> df.show
	+---------+---+
	|     name|age|
	+---------+---+
	|zhangwuji| 32|
	+---------+---+
	```
	
