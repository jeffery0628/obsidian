---
Create: 2021年 十二月 26日, 星期日 17:21
tags: 
  - Engineering/hadoop
  - 大数据
---





## Hadoop 组成
![[700 Attachments/Pasted image 20211226170618.png]]
在Hadoop1.x时代，Hadoop中的MapReduce同时处理业务逻辑运算和资源调度，耦合性较大，在Hadoop2.x时代，增加了Yarn。Yarn只负责资源调度，MapReduce只负责运算。
### HDFS 架构概述
HDFS包含两部分：
1. NameNode（NN）：存储文件的元数据，如文件名，文件目录结构，文件属性（生成时间、副本数、文件权限）,以及每个文件的块列表和块所在的DataNode等
2. DataNode（DN）：在本地文件系统存储文件块数据，以及块数据的校验
3. Secondary NameNode（2NN）：每隔一段时间对NameNode元数据备份。


### YARN 架构概述
YARN包含四个部分：
1. ResourceManager（RM）：
	1. 处理客户端请求
	2. 监控NodeManager
	3. 启动或监控ApplicationMaster
	4. 资源的分配与调度

2. NodeManager（NM）：
	1. 管理单个节点上的资源
	2. 处理来自ResourceManager的命令
	3. 处理来自ApplicationMaster的命令

3. ApplicationMaster（AM）：
	1. 负责数据的切分
	2. 为应用程序申请资源并分配给内部任务
	3. 任务的监控与容错

4. Container：Container是YARN中的资源抽象，它封装了某个节点上的多维度资源，如内存，CPU，磁盘，网络等。

### MapReduce架构概述
MapReduce 将计算过程分为两个阶段：Map 和 Reduce
1. Map阶段并行处理输入数据
2. Reduce阶段对Map结果进行汇总

![[700 Attachments/Pasted image 20211226172026.png]]



