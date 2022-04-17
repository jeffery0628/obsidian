---
Create: 2022年 四月 15日, 星期五 22:12
tags: 
  - Engineering/hive
  - 大数据
---
通常情况下，作业会通过input的目录产生一个或者多个map任务。
主要的决定因素有：input的文件总个数，input的文件大小，集群设置的文件块大小。


问1：是不是map数越多越好？
答案是否定的。如果一个任务有很多小文件（远远小于块大小128m），则每个小文件也会被当做一个块，用一个map任务来完成，而一个map任务启动和初始化的时间远远大于逻辑处理的时间，就会造成很大的资源浪费。而且，同时可执行的map数是受限的。

问2：是不是保证每个map处理接近128m的文件块，就高枕无忧了？
答案也是不一定。比如有一个127m的文件，正常会用一个map去完成，但这个文件只有一个或者两个小字段，却有几千万的记录，如果map处理的逻辑比较复杂，用一个map任务去做，肯定也比较耗时。

针对上面的问题1和2，我们需要采取两种方式来解决：即减少map数和增加map数；

## 复杂文件增加Map数
当input的文件都很大，任务逻辑复杂，map执行非常慢的时候，可以考虑增加Map数，来使得每个map处理的数据量减少，从而提高任务的执行效率。增加map的方法为：根据`computeSliteSize(Math.max(minSize,Math.min(maxSize,blocksize)))=blocksize=128M`公式，调整maxSize最大值。让maxSize最大值低于blocksize就可以增加map的个数。

## 小文件进行合并
在map执行前合并小文件，减少map数：CombineHiveInputFormat具有对小文件进行合并的功能（系统默认的格式）。HiveInputFormat没有对小文件合并功能。
```
set hive.input.format= org.apache.hadoop.hive.ql.io.CombineHiveInputFormat;
```

在Map-Reduce的任务结束时合并小文件的设置：
1. 在map-only任务结束时合并小文件，默认true:`SET hive.merge.mapfiles = true;`
2. 在map-reduce任务结束时合并小文件，默认false: `SET hive.merge.mapredfiles = true;`
3. 合并文件的大小，默认256M: `SET hive.merge.size.per.task = 268435456;`
4. 当输出文件的平均大小小于该值时，启动一个独立的map-reduce任务进行文件merge:`SET hive.merge.smallfiles.avgsize = 16777216;`

## 合理设置Reduce数
1. 调整reduce个数方法一
	1. 每个Reduce处理的数据量默认是256MB:`hive.exec.reducers.bytes.per.reducer=256000000`
	2. 每个任务最大的reduce数，默认为1009:`hive.exec.reducers.max=1009`
	3. 计算reducer数的公式: `N=min(参数2，总输入数据量/参数1)`

2. 调整reduce个数方法二
	在hadoop的mapred-default.xml文件中修改,设置每个job的Reduce个数:`set mapreduce.job.reduces = 15;`

3. reduce个数并不是越多越好?
	1. 过多的启动和初始化reduce也会消耗时间和资源；
	2. 有多少个reduce，就会有多少个输出文件，如果生成了很多个小文件，那么如果这些小文件作为下一个任务的输入，则也会出现小文件过多的问题；


	在设置reduce个数的时候也需要考虑这两个原则：处理大数据量利用合适的reduce数；使单个reduce任务处理数据量大小要合适；



