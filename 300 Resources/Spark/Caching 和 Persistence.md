---
Create: 2022年 三月 17日, 星期四 10:22
tags: 
  - Engineering/spark
  - 大数据
---

和 RDDs 类似，DStreams 同样允许开发者将流数据保存在内存中。也就是说，在DStream上使用persist()方法将会自动把DStreams中的每个RDD保存在内存中。
当DStream中的数据要被多次计算时，这个非常有用（如在同样数据上的多次操作）。对于像reduceByWindow和reduceByKeyAndWindow以及基于状态的(updateStateByKey)这种操作，保存是隐含默认的。因此，即使开发者没有调用persist()，由基于窗操作产生的DStreams也会自动保存在内存中。



