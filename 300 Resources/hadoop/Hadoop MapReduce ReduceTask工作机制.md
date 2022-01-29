---
Create: 2022年 一月 17日, 星期一 22:48
tags: 
  - Engineering/hadoop
  - 大数据
---
![[700 Attachments/Pasted image 20220117225159.png]]

1. Copy阶段：ReduceTask从各个MapTask上远程拷贝一片数据，并针对某一片数据，如果其大小超过一定阈值，则写到磁盘上，否则直接放到内存中。
2. Merge阶段：在远程拷贝数据的同时，ReduceTask启动了两个后台线程对内存和磁盘上的文件进行合并，以防止内存使用过多或磁盘上文件过多。
3. Sort阶段：按照MapReduce语义，用户编写reduce()函数输入数据是按key进行聚集的一组数据。为了将key相同的数据聚在一起，Hadoop采用了基于排序的策略。由于各个MapTask已经实现对自己的处理结果进行了局部排序，因此，ReduceTask只需对所有数据进行一次归并排序即可。
4. Reduce阶段：reduce()函数将计算结果写到HDFS上。


## ReduceTask并行度
ReduceTask的并行度同样影响整个Job的执行并发度和执行效率，但与MapTask的并发数由切片数决定不同，ReduceTask数量的是可以直接手动设置：
```java
// 默认值是1，手动设置为4
job.setNumReduceTasks(4);
```

1. ReduceTask=0，表示没有Reduce阶段，输出文件个数和Map个数一致。
2. ReduceTask 默认值就是1，所以输出文件个数为一个
3. 如果数据分布不均匀，就有可能在Reduce阶段产生数据倾斜
4. ReduceTask数量并不是任意设置，还要考虑业务逻辑需求，有些情况下，需要算全局汇总结果，就只能有1个ReduceTask。
5. 具体多少个ReduceTask，需要根据集群性能而定。
6. 如果分区数不是1，但是ReduceTask为1，是否执行分区过程？
	答案是：不执行分区过程。因为在MapTask的源码中，执行分区的前提是先判断ReduceNum个数是否大于1，不大于1肯定不执行。


