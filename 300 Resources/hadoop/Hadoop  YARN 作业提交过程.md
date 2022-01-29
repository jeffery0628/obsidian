---
Create: 2022年 一月 18日, 星期二 22:46
tags: 
  - Engineering/hadoop
  - 大数据
---

## YARN 作业提交过程
![[700 Attachments/Pasted image 20220118222658.png]]

作业提交全过程详解：
1. 作业提交
	1. 第1步：Client调用job.waitForCompletion方法，向整个集群提交MapReduce作业。
	2. 第2步：Client向RM申请一个作业id。
	3. 第3步：RM给Client返回该job资源的提交路径和作业id。
	4. 第4步：Client提交jar包、切片信息和配置文件到指定的资源提交路径。
	5. 第5步：Client提交完资源后，向RM申请运行MrAppMaster。
2. 作业初始化
	1. 第6步：当RM收到Client的请求后，将该job添加到容量调度器中。
	2. 第7步：某一个空闲的NM领取到该Job。
	3. 第8步：该NM创建Container，并产生MRAppmaster。
	4. 第9步：下载Client提交的资源到本地。
3. 任务分配
	1. 第10步：MrAppMaster向RM申请运行多个MapTask任务资源。
	2. 第11步：RM将运行MapTask任务分配给另外两个NodeManager，另两个NodeManager分别领取任务并创建容器。
4. 任务运行
	1. 第12步：MR向两个接收到任务的NodeManager发送程序启动脚本，这两个NodeManager分别启动MapTask，MapTask对数据分区排序。
	2. 第13步：MrAppMaster等待所有MapTask运行完毕后，向RM申请容器，运行ReduceTask。
	3. 第14步：ReduceTask向MapTask获取相应分区的数据。
	4. 第15步：程序运行完毕后，MR会向RM申请注销自己。
5. 进度和状态更新
	YARN中的任务将其进度和状态(包括counter)返回给应用管理器, 客户端每秒(通过mapreduce.client.progressmonitor.pollinterval设置)向应用管理器请求进度更新, 展示给用户。
6. 作业完成
	除了向应用管理器请求作业进度外, 客户端每5秒都会通过调用waitForCompletion()来检查作业是否完成。时间间隔可以通过mapreduce.client.completion.pollinterval来设置。作业完成之后, 应用管理器和Container会清理工作状态。作业的信息会被作业历史服务器存储以备之后用户核查。

==作业提交过程之MapReduce==：
![[700 Attachments/Pasted image 20220116215629.png]]
![[700 Attachments/Pasted image 20220116215101.png]]





