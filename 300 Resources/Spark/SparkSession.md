---
Create: 2022年 三月 17日, 星期四 13:19
tags: 
  - Engineering/spark
  - 大数据
---

# SparkSession 新的起始点
在老的版本中，SparkSQL提供两种SQL查询起始点：一个叫==SQLContext==，用于Spark自己提供的SQL查询；一个叫==HiveContext==，用于连接Hive的查询。

==SparkSession==是Spark最新的SQL查询起始点，实质上是SQLContext和HiveContext的组合，所以在SQLContex和HiveContext上可用的API在SparkSession上同样是可以使用的。SparkSession内部封装了sparkContext，所以计算实际上是由sparkContext完成的。当我们使用 spark-shell 的时候, spark 会自动的创建一个叫做spark的SparkSession, 就像我们以前可以自动获取到一个sc来表示SparkContext
![[700 Attachments/Pasted image 20220317132209.png]]




