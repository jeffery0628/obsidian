---
Create: 2022年 一月 14日, 星期五 10:15
tags: 
  - Engineering/hadoop
  - 大数据
---


## MapReduce的数据流
![[700 Attachments/Pasted image 20220114103346.png]]

## 切片与MapTask并行度决定机制
问题引出:
MapTask的并行度决定Map阶段的任务处理并发度，进而影响到整个Job的处理速度。
思考：
1G的数据，启动8个MapTask，可以提高集群的并发处理能力。那么1K的数据，也启动8个MapTask，会提高集群性能吗？MapTask并行任务是否越多越好呢？哪些因素影响了MapTask并行度？

MapTask 并行度决定机制:
==数据块==:Block是HDFS物理上把数据分成一块一块。
==数据切片==：数据切片只是在逻辑上对输入进行分片，并不会再磁盘上讲其切分成片进行存储。

1. 一个job的map阶段并行度由客户端在提交job时决定
2. 每一个split切片分配一个mapTask并行实例处理
3. 默认情况下，切片大小=blocksize
4. 切片时不考虑数据集整体，而是逐个针对每一个文件单独切片

假设切片大小设置为100M：
![[700 Attachments/Pasted image 20220116191136.png]]

> 切片大小小于BlockSize，会产生不必要的数据传输，一般将切片大小设置为BlockSize

![[700 Attachments/Pasted image 20220116191603.png]]

## Job提交流程
![[700 Attachments/Pasted image 20220116192853.png]]

1. 建立连接
	1. 创建提交Job代理
	2. 判断是本地还是远程
2. 提交job
	1. 创建给集群提交数据的stag路径
	2. 获取jobid，并创建Job路径
	3. 拷贝jar包到集群
	4. 计算切片，生成切片规划文件
	5. 向stag路径写XML配置文件
	6. 提交Job，返回提交状态



## FileInputFormat切片过程
1. 程序先找到数据存储的目录
2. 遍历处理（规划切片）目录下的每一个文件
3. 遍历第一个文件xxx.txt：
	1. 获取文件大小：`fs.sizeOf(xxx.txt)`
	2. 计算切片：`computeSplitSize(Math.max(minSize,Math.min(maxSize,blocksize))) = blocksize = 128M`
	3. 默认情况下，切片大小=blocksize
	4. 开始切片，形成第一个切片：xxx.txt--> 0：128M
							 形成第二个切片：128：256M
							 形成第三个切片：256：300M
		 > 每次切片时，都要判断切完剩下的部分是否大于块的1.1倍，不大于1.1倍就划分一块切片
	5. 将切片信息写到一个切片规划文件中
	6. 整个切片的核心过程在`getSplit()`方法中完成
	7. InputSplit只记录了切片的元数据信息，比如起始位置、长度以及所在的节点列表等。
4. 提交切片规划文件到YARN上，YARN上的MrAppMaster可以根据切片规划文件计算开启MapTask个数。


### FileInputFormat切片机制
切片机制：
1. 简单地按照文件的内容长度进行切片
2. 切片大小，默认等于Block大小
3. 切片时==不考虑数据集整体==，而是逐个对每一个文件单独切片

案例：
输入数据集有两个文件：
file1.txt	320M
file2.txt	10M

经过FileInputFormat的切片机制运算后，形成的切片信息如下：
file1.txt.split1 -- 0~128M
file1.txt.split2 -- 128~256M
file1.txt.split3 -- 256~320M
file2.txt.split1 -- 0~10M


### FileInputFormat切片大小的参数配置
#### 源码中计算切片大小的公式
```java
Math.max(minSize,Math.min(maxSize,blocksize));
mapreduce.input.fileinputformat.split.minsize = 1; // 默认值为1
mapreduce.input.fileinputformat.split.maxsize = Long.MAXVALUE
```
因此默认情况下，切片大小为blocksize

#### 切片大小设置
maxsize：切片最大值，参数如果调的比blockSize小，则会让切片变小，而且就等于配置的这个参数的值。
minsize：切片最小值，参数调的比blockSize大，则可以让切片边得比blocksize还打。

#### 切片信息获取
```java
// 获取切片的文件名称
String name = inputSplit.getPath().getName();
// 根据文件类型获取切片信息
FileSplit inputSplit = (FileSplit)context.getInputSplit();
```

## CombineTextInputFormat切片机制
==应用场景==
框架默认的TextInputFormat切片机制是对任务按文件规划切片，不管文件多小，都会是一个单独的切片，都会交给一个MapTask，这样如果有大量小文件，就会产生大量的MapTask，处理效率极其低下。

CombineTextInputFormat用于小文件过多的场景，它可以将多个小文件从逻辑上规划到一个切片中，这样，多个小文件就可以交给一个MapTask处理。

==虚拟存储切片最大值设置==
```jav
CombineTextInputFormat.setMaxInputSplitSize(job, 4194304);// 4m
```
> 注意：虚拟存储切片最大值设置最好根据实际的小文件大小情况来设置具体的值。

### 切片机制
![[700 Attachments/Pasted image 20220116195957.png]]

==虚拟存储过程==：
将输入目录下所有文件大小，依次和设置的setMaxInputSplitSize值比较，如果不大于设置的最大值，逻辑上划分一个块。如果输入文件大于设置的最大值且大于两倍，那么以最大值切割一块；当剩余数据大小超过设置的最大值且不大于最大值2倍，此时将文件均分成2个虚拟存储块（防止出现太小切片）。

例如setMaxInputSplitSize值为4M，输入文件大小为8.02M，则先逻辑上分成一个4M。剩余的大小为4.02M，如果按照4M逻辑划分，就会出现0.02M的小的虚拟存储文件，所以将剩余的4.02M文件切分成（*2.01M和2.01M*）两个文件。

==切片过程==：
1. 判断虚拟存储的文件大小是否大于setMaxInputSplitSize值，大于等于则单独形成一个切片。
2. 如果不大于则跟下一个虚拟存储文件进行合并，共同形成一个切片。
3. 测试举例：有4个小文件大小分别为1.7M、5.1M、3.4M以及6.8M这四个小文件，则虚拟存储之后形成6个文件块，大小分别为：1.7M，（2.55M、2.55M），3.4M以及（3.4M、3.4M）最终会形成3个切片，大小分别为：(1.7+2.55）M，（2.55+3.4）M，（3.4+3.4）M



### wordCount实操
在`WordcountDriver`中新增如下代码:
```java
// 新增设置切片个数
job.setInputFormatClass(CombineTextInputFormat.class); // 如果不设置InputFormat，它默认用的是TextInputFormat.class
CombineTextInputFormat.setMaxInputSplitSize(job,4194304); // 设置最大为4M
```


## TextInputFormat的KV
`TextInputFormat`是默认的`FileInputFormat`实现类。按行读取每条记录。键值存储改行在整个文件中的起始字节偏移量，LongWritable类型。值是改行的内容，不包括任何行终止符（换行符和回车符），Text类型。
以下是一个示例：
==文本==：
```
Rich learning form
Intelligent learning engine
Learning more convenient
From the real demand for more close to the enterprise
```
每条记录表示为以下键/值对
```
(0,Rich learning form)
(19,Intelligent learning engine)
(47,Learning more convenient)
(72,From the real demand for more close to the enterprise)
```