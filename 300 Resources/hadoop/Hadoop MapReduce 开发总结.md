---
Create: 2022年 一月 18日, 星期二 13:45
tags: 
  - Engineering/hadoop
  - 大数据
---

## InputFormat
1. 默认使用的实现类是TextInputFormat
2. TextInputFormat的功能逻辑是：一次读取一行文本，然后将该行的起始偏移量作为key，行内容作为value返回
3. CombineTextInputFormat可以把多个小文件合并成一个切片处理，提高处理效率。


## Mapper
用户根据业务需求实现其中三个方法
1. map
2. setup
3. cleanup

## Partitioner
1. 有默认实现HashPartitioner，逻辑是根据key的哈希值和numReduces来返回一个分区号；`(key.hashCode() && Integer.MAXVALUE) % numReduces`
2. 如果业务上有特别的需求，可以自定义分区。

## Comparable排序
1. 当我们用自定义的对象作为key来输出时，就必须要实现WritableComparable接口，重写其中的compareTo()方法。
2. 部分排序：对最终输出的每一个文件进行内部排序。
3. 全排序：对所有数据进行排序，通常只有一个Reduce。
4. 二次排序：排序的条件有两个

> Combiner 合并可以提高程序执行效率，减少IO传输。但是使用时必须不能影响原有的业务处理结果。

## Reducer
用户根据业务需求实现其中三个方法：
1. reduce
2. setup
3. cleanup


## outputFormat
1. 默认实现类是TextOutputFormat，功能逻辑是：将每一个KV对，向目标文本文件输出一行。
2. 将SequenceFileOutputFormat输出作为后续MapReduce任务的输入，这便是一种好的输出格式，因为它格式紧凑，很容易被压缩。
3. 用户还可以自定义OutputFormat。




