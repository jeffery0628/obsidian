---
Create: 2021年 十二月 1日, 星期三 13:17
tags: 
  - Engineering/spark
  - 大数据
---
## Master

Spark 特有资源调度系统的 Leader。掌管着整个集群的资源信息，类似于 Yarn 框架中的 ResourceManager，主要功能：

1.  监听 Worker，看 Worker 是否正常工作；
    
2.  Master 对 Worker、Application 等的管理(接收 Worker 的注册并管理所有的Worker，接收 Client 提交的 Application，调度等待的 Application 并向Worker 提交)。
    

## worker

Spark 特有资源调度系统的 Slave，有多个。每个 Slave 掌管着所在节点的资源信息，类似于 Yarn 框架中的 NodeManager，主要功能：

1.  通过 RegisterWorker 注册到 Master；
2.  定时发送心跳给 Master； 
3.  根据 Master 发送的 Application 配置进程环境，并启动 ExecutorBackend(执行 Task 所需的临时进程)
    

## driver program

每个 Spark 应用程序都包含一个**驱动程序**, 驱动程序负责把并行操作发布到集群上。驱动程序包含 Spark 应用程序中的**主函数**, 定义了分布式数据集以应用在集群中。

驱动程序通过`SparkContext`对象来访问 Spark, `SparkContext`对象相当于一个到 Spark 集群的连接.

在 spark-shell 中, 会自动创建一个`SparkContext`对象, 并把这个对象命名为**sc**.

## executor

`SparkContext`对象一旦成功连接到集群管理器, 就可以获取到集群中每个节点上的执行器(**executor**)。

执行器是一个进程(进程名: ExecutorBackend, 运行在 Worker 节点上), 用来`执行计算`和`为应用程序存储数据`；

Spark 会发送应用程序代码(比如jar包)到每个执行器；`SparkContext`对象发送任务到执行器开始执行程序。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/spark-install/image-20210919143246099.png)

## RDD

RDDs:即，Resilient Distributed Dataset， 弹性分布式数据集。

一旦拥有了SparkContext对象, 就可以使用它来创建 RDD 了。

## cluster managers

为了在一个 Spark 集群上运行计算, SparkContext对象可以连接到几种集群管理器：

1.  Spark’s own standalone cluster manager
    
2.  Mesos
    
3.  YARN




