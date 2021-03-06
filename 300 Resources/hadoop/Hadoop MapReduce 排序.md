---
Create: 2022年 一月 17日, 星期一 10:04
tags: 
  - Engineering/hadoop
  - 大数据
---

## 排序概述
排序是MapReduce框架中最重要的操作之一。

MapTask和ReduceTask均会对数据==按照key==进行排序。该操作属于Hadoop的默认行为。任何应用程序中的数据均会被排序，而不管逻辑上是否需要。

默认排序是按照==字典顺序==排序，且实现该排序的方法是==快速排序==。

对于==MapTask==，它会将处理的结果暂时放到环形缓冲区中，当环形缓冲区使用率达到一定阈值后，再对缓冲区中的数据进行一次快速排序，并将这些有序数据溢写到磁盘上，而当数据处理完毕后，它会对磁盘上所有文件进行归并排序。

对于==ReduceTask==，它从每个MapTask上远程拷贝相应的数据文件,如果文件大小超过一定阈值,则溢写磁盘上,否则存储在内存中。如果内存中文件大小或者数目超过一定阈值，则进行一次合并后将数据溢写到磁盘上。当所有数据拷贝完毕后，ReduceTask统一对内存和磁盘上的所有数据进行一次归并排序。


### 排序分类
1. 部分排序：MapReduce根据输入记录的键值对数据集排序。保证输出的每个文件内部有序。
2. 全排序：最终输出结果只有一个文件，且文件内部有序。实现方式是只设置一个ReduceTask。但该方法在处理大型文件时效率极低，因为一台机器处理所有文件，完全丧失了MapReduce所提供的并行架构。
3. 辅助排序(GroupingComparator分组)：在Reduce端对key进行分组。应用于：在接收的key为bean对象时，想让一个或几个字段相同（全部字段比较不相同）的key进入到同一个reduce方法时可以采用分组排序。
4. 二次排序：在自定义排序过程中，如果compareTo中的判断条件为两个即为二次排序。


### 自定义排序WritableComparable
bean对象做为key传输，需要实现==WritableComparable==接口重写compareTo方法，就可以实现排序。
```java
@Override
public int compareTo(FlowBean o) {

	int result;
		
	// 按照总流量大小，倒序排列
	if (sumFlow > bean.getSumFlow()) {
		result = -1;
	}else if (sumFlow < bean.getSumFlow()) {
		result = 1;
	}else {
		result = 0;
	}

	return result;
}

```


### WritableComparable 区内排序
==需求==：要求每个省份手机号输出的文件中按照总流量内部排序。
==需求分析==：基于前一个需求，增加自定义分区类，分区按照省份手机号设置。
==输入数据==：
```
13509468723	7335	110349	117684
13975057813	11058	48243	59301
13568436656	3597	25635	29232
13736230513	2481	24681	27162
18390173782 9531	2412	11943
13630577991	6960	690	7650
15043685818	3659	3538	7197
13992314666	3008	3720	6728
15910133277	3156	2936	6092
13560439638	918	4938	5856
84188413	4116	1432	5548
13682846555	1938	2910	4848
18271575951	1527	2106	3633
15959002129	1938	180	2118
13590439668	1116	954	2070
13956435636	132	1512	1644
13470253144	180	180	360
13846544121	264	0	264
13966251146	240	0	240
13768778790	120	120	240
13729199489	240	0	240
```
==期望输出==：
```
part-r-00000
	13630577991 6960	690	7650
	13682846555	1938	2910	4848
part-r-00001
	13736230513 2481	24681	27162
	13768778790	120	120	240
	13729199489	240	0	240
part-r-00002
	13846544121 264	0	264
part-r-00003
	13975057813 11058	48243	59301
	13992314666 3008	3720	6728
	13956435636	132	1512	1644
	13966251146	6240	0	240
part-r-00004
	13509468723 7335	110349	117684
	13568436656 3597	25635	29232
	18390173782 9531	2412	11943
	15043685818	3659	3538	7197
	15910133277	3156	2936	6092
```

#### 自定义分区类
```java
public class ProvincePartitioner extends Partitioner<FlowBean, Text> {

	@Override
	public int getPartition(FlowBean key, Text value, int numPartitions) {
		
		// 1 获取手机号码前三位
		String preNum = value.toString().substring(0, 3);
		
		int partition = 4;
		
		// 2 根据手机号归属地设置分区
		if ("136".equals(preNum)) {
			partition = 0;
		}else if ("137".equals(preNum)) {
			partition = 1;
		}else if ("138".equals(preNum)) {
			partition = 2;
		}else if ("139".equals(preNum)) {
			partition = 3;
		}

		return partition;
	}
}

```

#### 驱动类中新增代码
```java
/ 加载自定义分区类
job.setPartitionerClass(ProvincePartitioner.class);

// 设置Reducetask个数
job.setNumReduceTasks(5);
```

