---
Create: 2022年 三月 15日, 星期二 13:28
tags: 
  - Engineering/spark
  - 大数据
---
# 简介

广播变量：分布式共享只读变量。
在多个并行操作中（Executor）使用同一个变量，Spark默认会为每个任务(Task)分别发送，这样如果共享比较大的对象，会占用很大工作节点的内存。
广播变量用来高效分发较大的对象。向所有工作节点发送一个较大的只读值，以供一个或多个Spark操作使用。比如，如果你的应用需要向所有节点发送一个较大的只读查询表，甚至是机器学习算法中的一个很大的特征向量，广播变量用起来都很顺手。

## 使用广播变量步骤：
1. 通过对一个类型T的对象调用SparkContext.broadcast创建出一个Broadcast[T]对象，任何可序列化的类型都可以这么实现。
2. 通过value属性访问该对象的值（在Java中为value()方法）。
3. 变量只会被发到各个节点一次，应作为只读值处理（修改这个值不会影响到别的节点）。

```scala
object broadcast {
    def main(args: Array[String]): Unit = {
        val conf: SparkConf = new SparkConf().setAppName("SparkCoreTest").setMaster("local[*]")
        val sc: SparkContext = new SparkContext(conf)

        //3.2 采用集合的方式，实现rdd1和list的join
        val rdd1: RDD[(String, Int)] = sc.makeRDD(List(("a", 1), ("b", 2), ("c", 3)),2)
        val list: List[(String, Int)] = List(("a", 4), ("b", 5), ("c", 6))

        // 声明广播变量
        val broadcastList: Broadcast[List[(String, Int)]] = sc.broadcast(list)

        val resultRDD: RDD[(String, (Int, Int))] = rdd1.map {
            case (k1, v1) => {
                var v2: Int = 0
                // 使用广播变量
                //for ((k3, v3) <- list.value) {
                for ((k3, v3) <- broadcastList.value) {
                    if (k1 == k3) {
                        v2 = v3
                    }
                }
                (k1, (v1, v2))
            }
        }
        resultRDD.foreach(println)
        //4.关闭连接
        sc.stop()
    }
}


```
![[700 Attachments/Pasted image 20220315133701.png]]





