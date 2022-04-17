---
Create: 2022年 三月 17日, 星期四 10:09
tags: 
  - Engineering/spark
  - 大数据
---

输出操作指定了对流数据经转化操作得到的数据所要执行的操作(例如把结果推入外部数据库或输出到屏幕上)。与RDD中的惰性求值类似，如果一个DStream及其派生出的DStream都没有被执行输出操作，那么这些DStream就都不会被求值。如果StreamingContext中没有设定输出操作，整个context就都不会启动。

# 常用输出操作

## print
在运行流程序的驱动结点上打印DStream中每一批次数据的最开始10个元素。这用于开发和调试。在Python API中，同样的操作叫print()。


## saveAsTextFiles
```scala
saveAsTextFiles(prefix, [suffix])
```
以text文件形式存储这个DStream的内容。每一批次的存储文件名基于参数中的prefix和suffix。”prefix-Time_IN_MS\[.suffix\]”。

## saveAsObjectFiles
```scala
saveAsObjectFiles(prefix, [suffix])
```
以Java对象序列化的方式将Stream中的数据保存为 SequenceFiles . 每一批次的存储文件名基于参数中的为"prefix-TIME_IN_MS\[.suffix\]". Python中目前不可用。


## saveAsHadoopFiles

```scala
saveAsHadoopFiles(prefix, [suffix])
```
将Stream中的数据保存为 Hadoop files. 每一批次的存储文件名基于参数中的为"prefix-TIME_IN_MS\[.suffix\]"。Python API 中目前不可用。

## foreachRDD
```scala
foreachRDD(func)
```
这是最通用的输出操作，即将函数 func 用于产生于 stream的每一个RDD。其中参数传入的函数func应该实现将每一个RDD中数据推送到外部系统，如将RDD存入文件或者通过网络将其写入数据库。
通用的输出操作foreachRDD()，它用来对DStream中的RDD运行任意计算。这和transform() 有些类似，都可以让我们访问任意RDD。在foreachRDD()中，可以重用在Spark中实现的所有行动操作。比如，常见的用例之一是把数据写到诸如MySQL的外部数据库中，但是在使用的时候需要注意以下几点
- 连接不能写在driver层面（序列化）；
- 如果写在foreach则每个RDD中的每一条数据都创建，得不偿失；
- 增加foreachPartition，在分区创建（获取）。


