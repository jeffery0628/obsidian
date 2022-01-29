---
Create: 2022年 一月 18日, 星期二 13:36
tags: 
  - Engineering/hadoop
  - 大数据
---
## 计数器应用
Hadoop为每个作业维护若干内置计数器，以描述多项指标。例如，某些计数器记录已处理的字节数和记录数，使用户可以监控已处理的输入数据量和已生产的输出数据量。

## API
1. 采用枚举的方式统计计数
	```java
	enum MyCounter{
		MALFORORMED,NORMAL
	}
	context.getCounter(MyCounter.MALFORORMED).increment(1);

	```

2. 采用计数器组、计数器名称的方式统计
	```java
	context.getCounter("counterGroup","counter").increment(1);
	```

3. 计数结果在程序运行后的控制台上查看



