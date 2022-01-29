---
Create: 2022年 一月 17日, 星期一 13:38
tags: 
  - Engineering/hadoop
  - 大数据
---
## 概述
1. Combiner是MapReduce程序中Mapper 和 Reducer之外的一种组件；
2. Combiner组件的父类就是==Reducer==；
3. Combiner和Reducer的区别在于运行的位置：Combiner是在每一个MapTask所在的节点运行，Reducer是接收全局所有Mapper的输出结果。
4. Combiner的意义就是对每一个MapTask的输出进行==局部汇总==，以减小网络传输量。
5. Combiner能够应用的前提是不能影响最终的业务逻辑，而且，Combiner的输出KV应该跟Reducer的输入kv类型要对应起来。
	```
	Mapper								Reducer
	3 5 7 -> (3+5+7) / 3 = 5			3 5 7 2 6 ->(3+5+7+2+6)/5 = 23/5 
	2 6 -> (2 + 6) / 2 = 4
	
	```


## 自定义Combiner
自定义一个Combiner继承Reducer，重写Reduce方法

### WordcountCombiner
```java
public class WordcountCombiner extends Reducer<Text, IntWritable, Text,IntWritable>{

	@Override
	protected void reduce(Text key, Iterable<IntWritable> values,Context context) throws IOException, InterruptedException {

        // 1 汇总操作
		int count = 0;
		for(IntWritable v :values){
			count += v.get();
		}

        // 2 写出
		context.write(key, new IntWritable(count));
	}
}


```

在Job驱动类中设置:
```java
job.setCombinerClass(WordcountCombiner.class);
```

## 案例实操
==需求==：统计过程中对每一个MapTask的输出进行局部汇总，以减小网络传输量即采用Combiner功能。

==需求分析==：
输入数据：
```java
banzhang ni hao
xihuan xuexi hadoop
banzhang
bangzhao xihuan
xuexi hadoop
banzhang
```

期望输出：
![[700 Attachments/Pasted image 20220117222056.png]]

方案一：
1. 增加一个WordCountCombiner类继承Reducer
2. 在WordCountCombiner中
	1. 统计单词汇总
	2. 将统计结果输出

```java

public class WordcountCombiner extends Reducer<Text, IntWritable, Text, IntWritable>{

IntWritable v = new IntWritable();

	@Override
	protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {

        // 1 汇总
		int sum = 0;

		for(IntWritable value :values){
			sum += value.get();
		}

		v.set(sum);

		// 2 写出
		context.write(key, v);
	}
}
```
在WordcountDriver驱动类中指定Combiner:
```java
// 指定需要使用combiner，以及用哪个类作为combiner的逻辑
job.setCombinerClass(WordcountCombiner.class);
```



方案二：
1.将WordCountReducer作为Combiner在WordCountDriver驱动类中指定`job.setCombinerClass(WordCountReducer.class)`


![[700 Attachments/Pasted image 20220117222439.png]]

