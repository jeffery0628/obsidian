---
Create: 2021年 十二月 1日, 星期三 09:28
tags: 
  - Engineering/spark
  - 大数据
---


# Spark 特点

## 快速

与 Hadoop 的 MapReduce 相比, Spark 基于内存的运算是 MapReduce 的 `100`倍.基于硬盘的运算也要快 `10` 倍以上.

Spark 实现了高效的 DAG 执行引擎, 可以通过基于内存来高效处理数据流。

## 易用

Spark 支持 Scala, Java, Python, R 和 SQL 脚本, 并提供了超过 80 种高性能的算法, 非常容易创建并行 App。而且 Spark 支持交互式的 Python 和 Scala 的 shell, 这意味着可以非常方便地在这些 shell 中使用 Spark 集群来验证解决问题的方法, 而不是像以前一样需要打包, 上传集群, 验证等。

## 通用

Spark 结合了SQL, Streaming和复杂分析。Spark 提供了大量的类库, 包括 SQL 和 DataFrames, 机器学习(MLlib), 图计算(GraphicX), 实时流处理(Spark Streaming) .可以把这些类库无缝的柔和在一个 App 中。减少了开发和维护的人力成本以及部署平台的物力成本。

## 可融合性

Spark 可以非常方便的与其他开源产品进行融合。比如, Spark 可以使用 Hadoop 的 YARN 和 Appache Mesos 作为它的资源管理和调度器, 并且可以处理所有 Hadoop 支持的数据, 包括 HDFS, HBase等.



# Hadoop MR 框架 和 Spark 框架 
## Hadoop MR 框架
从数据源获取数据，经过分析计算后，将结果输出到指定位置，核心是==一次计算==，不适合迭代计算。
![[700 Attachments/Pasted image 20220308133007.png]]
## Spark 框架
==支持迭代计算，图形计算==。Spark框架计算比MR快的原因是：中间结果不落盘。
> 注意：Spark的Shuffle也是落盘的。

![[700 Attachments/Pasted image 20220308133204.png]]

