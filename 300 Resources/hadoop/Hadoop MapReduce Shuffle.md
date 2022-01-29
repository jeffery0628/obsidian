---
Create: 2022年 一月 16日, 星期日 21:59
tags: 
  - Engineering/hadoop
  - 大数据
---
## shuffle 机制
Map方法之后，Reduce方法之前的数据处理过程称之为==Shuffle==。
![[700 Attachments/Pasted image 20220116223254.png]]


### Partition 分区
==问题引出==
要求将统计结果按照条件输出到不同文件中（分区）。比如:将统计结果按照手机归属地不同省份输出到不同文件中

默认分区代码：
```java
public class  HashParitioner<K,V> extends Partitioner<K,V>{

	@Override
	public int getPartition(K key, V value, int numReduceTasks) {
		return (key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
	}
}

```

默认分区是根据key的hashCode对ReduceTasks个数取模得到的。用户没法控制哪个key存储到哪个分区。

==自定义Partitioner步骤==:
1. 自定义类继承Partitioner，重写getPartition()方法：
	```java
    public class  CustomParitioner extends  Partitioner<Text, FlowBean>{
        @Override
        public int getPartition(Text text, FlowBean flowBean, int numPartitions) {
            // 控制分区代码逻辑
            
            return partition;
        }
    }
	```
2. 在Job驱动中，设置自定义Partitioner
	```java
	job.setPartitionerClass(CustomPartitioner.class)
	```
3. 自定义Partition后，要根据自定义Partitioner的逻辑设置相应数量的ReduceTask
	```java
	job.setNumReduceTasks(5);
	```
	
==分区总结==:
1. 如果ReduceTask的数量 > getPartition的结果数，则会多产生几个空的输出文件part-r-000xx;
2. 如果1 < ReduceTask的数量 < getPartition的结果数，则有一部分分区数据无处安放，会Exception；
3. 如果ReduceTask的数量=1，则不管MapTask端输出多少个分区文件，最终结果都交给这一个ReduceTask，最终也就只会产生一个结果文件part-r-00000；
4. 分区号必须从零开始，逐一累加；

假如自定义分区数为5，则：
1. job.setNumReduceTask(1); 会正常运行，只不过会产生一个输出文件
2. job.setNumReduceTask(2); 会报错
3. job.setNumReduceTask(6); 大于5，程序会正常运行，会产生空文件。


#### 案例实操
需求：将统计结果按照手机归属地不同省份输出到不同文件中（分区），手机号136、137、138、139开头都分别放到一个独立的4个文件中，其他开头的放到一个文件中。

输入输出：
```
输入							期望输出：
13630577991	6960	690			文件1
13736230513	2481	24681		文件2
13846544121	264		0			文件3
13956435636	132		1512		文件4
13560439638	918		4938		文件5
```

增加一个==ProvincePartitioner==分区
```
136		分区0
137		分区1
138		分区2
139		分区3
其他	   分区4
```

Driver驱动类：
1. 指定自定义数据分区 `job.setPartitionerClass(ProvincePartitioner.class)`
2. 指定相应数量的reduceTask：`job.setNumReduceTasks(5)`

##### 分区类
```java
public class ProvincePartitioner extends Partitioner<Text, FlowBean> {

    @Override
    public int getPartition(Text key, FlowBean value, int numPartitioners) {
        // 1. 获取电话号码前三位
        String preNum = key.toString().substring(0, 3);
        int partition = 4;

        // 2. 判断是哪个省
        if ("136".equals(preNum)) {
            partition = 0;
        } else if ("137".equals(preNum)) {
            partition = 1;
        } else if ("138".equals(preNum)) {
            partition = 2;
        } else if ("139".equals(preNum)) {
            partition = 3;
        }
        return partition;
    }
}

```

##### 驱动函数中增加自定义数据分区设置 和 ReduceTask设置
```java
// 8 指定自定义数据分区
job.setPartitionerClass(ProvincePartitioner.class);

// 9 同时指定相应数量的reduce task
job.setNumReduceTasks(5);

```