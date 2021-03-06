---
Create: 2022年 三月 17日, 星期四 10:24
tags: 
  - Engineering/spark
  - 大数据
---


# 概述
Spark SQL是Spark用于结构化数据(structured data)处理的Spark模块。

与基本的Spark RDD API不同，Spark SQL的抽象数据类型为Spark提供了关于数据结构和正在执行的计算的更多信息。
在内部，Spark SQL使用这些额外的信息去做一些额外的优化，有多种方式与Spark SQL进行交互，比如: SQL和DatasetAPI。

当计算结果的时候，使用的是相同的执行引擎，不依赖你正在使用哪种API或者语言。这种统一也就意味着开发者可以很容易在不同的API之间进行切换，这些API提供了最自然的方式来表达给定的转换。

我们已经学习了Hive，它是将Hive SQL转换成 MapReduce然后提交到集群上执行，大大简化了编写 MapReduce的程序的复杂性，由于MapReduce这种计算模型执行效率比较慢。

所以Spark SQL的应运而生，它是将Spark SQL转换成RDD，然后提交到集群执行，执行效率非常快！

Spark SQL它提供了2个编程抽象，类似Spark Core中的RDD
- DataFrame
- DataSet

# Spark Sql 特点

## 易整合
无缝的整合了 SQL 查询和 Spark 编程
```scala
context = HiveContext(sc)
results = context.sql("select * from people")
names = results.map(lambda p : p.name)
```

## 统一的数据访问方式
使用相同的方式连接不同的数据源
```scala
context.jsonFile("s3n://....").registerTemplate("json")
result = context.sql("
	SELECT *
	FROM people
	JOIN json ...
")
```

## 兼容Hive
在已有的仓库上直接运行 SQL 或者 HiveQL
![[700 Attachments/Pasted image 20220317103045.png]]

## 标准的数据连接
通过 JDBC 或者 ODBC 来连接
![[700 Attachments/Pasted image 20220317103109.png]]

# 什么是DataFrame
在Spark中，DataFrame是一种以RDD为基础的分布式数据集，类似于传统数据库中的二维表格。DataFrame与RDD的主要区别在于，前者带有schema元信息，即DataFrame所表示的二维表数据集的每一列都带有名称和类型。这使得Spark SQL得以洞察更多的结构信息，从而对藏于DataFrame背后的数据源以及作用于DataFrame之上的变换进行了针对性的优化，最终达到大幅提升运行时效率的目标。反观RDD，由于无从得知所存数据元素的具体内部结构，Spark Core只能在stage层面进行简单、通用的流水线优化。

同时，与Hive类似，DataFrame也支持嵌套数据类型（struct、array和map）。从 API 易用性的角度上看，DataFrame API提供的是一套高层的关系操作，比函数式的RDD API 要更加友好，门槛更低。
![[700 Attachments/Pasted image 20220317130751.png]]
上图直观地体现了DataFrame和RDD的区别。

左侧的RDD\[Person\]虽然以Person为类型参数，但Spark框架本身不了解Person类的内部结构。而右侧的DataFrame却提供了详细的结构信息，使得 Spark SQL 可以清楚地知道该数据集中包含哪些列，每列的名称和类型各是什么。
DataFrame是为数据提供了Schema的视图。可以把它当做数据库中的一张表来对待
DataFrame也是懒执行的，但性能上比RDD要高，主要原因：优化的执行计划，即查询计划通过Spark catalyst optimiser进行优化。比如下面一个例子:
```scala
users.join(events,users("id") == events("uid")).filter(events("date") > "2015-01-01")
```
![[700 Attachments/Pasted image 20220317131302.png]]

为了说明查询优化，我们来看上图展示的人口数据分析的示例。图中构造了两个DataFrame，将它们join之后又做了一次filter操作。
如果原封不动地执行这个执行计划，最终的执行效率是不高的。因为join是一个代价较大的操作，也可能会产生一个较大的数据集。如果我们能将filter下推到 join下方，先对DataFrame进行过滤，再join过滤后的较小的结果集，便可以有效缩短执行时间。而Spark SQL的查询优化器正是这样做的。简而言之，逻辑查询计划优化就是一个利用基于关系代数的等价变换，将高成本的操作替换为低成本操作的过程。 
![[700 Attachments/Pasted image 20220317131359.png]]
# 什么是DataSet
DataSet是分布式数据集合。DataSet是Spark 1.6中添加的一个新抽象，是DataFrame的一个扩展。它提供了RDD的优势（强类型，使用强大的lambda函数的能力）以及Spark SQL优化执行引擎的优点。DataSet也可以使用功能性的转换（操作map，flatMap，filter等等）。
- 是DataFrame API的一个扩展，是SparkSQL最新的数据抽象
- 用户友好的API风格，既具有类型安全检查也具有DataFrame的查询优化特性；
- 用样例类来定义DataSet中数据的结构信息，样例类中每个属性的名称直接映射到DataSet中的字段名称；
- DataSet是强类型的。比如可以有DataSet\[Car\]，DataSet\[Person\]。
- DataFrame是DataSet的特列，DataFrame=DataSet\[Row\] ，所以可以通过as方法将DataFrame转换为DataSet。Row是一个类型，跟Car、Person这些的类型一样，所有的表结构信息都用Row来表示。

![[700 Attachments/Pasted image 20220317131851.png]]

