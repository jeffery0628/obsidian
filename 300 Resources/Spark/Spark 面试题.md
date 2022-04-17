---
Create: 2022年 三月 15日, 星期二 13:38
tags: 
  - Engineering/spark
  - 大数据
---
1. 编写WordCount(读取一个本地文件)，并打包到集群运行，说明需要添加的主要参数

	```scala
	sc.textFile("hdfs://linux1:9000/test.log").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect
	```
2. RDD的五个主要特性:分区器、首选位置、计算方法、依赖关系、分区
3. 如何创建一个RDD，有几种方式，举例说明
4. 创建一个RDD，使其一个分区的数据转变为一个String。例如(Array("a","b","c","d"),2)=>("ab","cd")
5. map与mapPartitions的区别
6. coalesce与repartition两个算子的作用以及区别与联系
7. 使用zip算子时需要注意的是什么（即哪些情况不能使用）
8. reduceByKey跟groupByKey之间的区别。
9. reduceByKey跟aggregateByKey之间的区别与联系。
10. combineByKey的参数作用，说明其参数调用时机。
11. 使用RDD实现Join的多种方式。
12. aggregateByKey与aggregate之间的区别与联系。
13. 创建一个RDD，自定义一种分区规则并实现？spark中是否可以按照Value分区。
14. 读取文件，实现WordCount功能。（使用不同的算子实现，至少3种方式）
15. 说说你对RDD血缘关系的理解。
16. Spark是如何进行任务切分的，请说明其中涉及到的相关概念。
17. RDD的cache和checkPoint的区别和联系。
18. 创建一个RDD，自定义一种分区规则并实现。
19. Spark读取HDFS文件默认的切片机制。
20. 说说你对广播变量的理解。
21. 自定义一个累加器，实现计数功能。





