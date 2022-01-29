---
Create: 2022年 一月 19日, 星期三 13:43
tags: 
  - Engineering/hadoop
  - 大数据
---

## 集群间数据拷贝
1. scp实现两个远程主机之间的文件复制
2. 采用distcp命令实现两个Hadoop集群之间的递归数据复制

## 小文件存档
==HDFS存储小文件弊端==：每个文件均按块存储，每个块的元数据存储在NameNode的内存中，因此HDFS存储小文件会非常低效。因为大量的小文件会耗尽NameNode中的大部分内存。但注意，存储小文件所需要的磁盘容量和数据块的大小无关。例如，一个1MB的文件设置为128MB的块存储，实际使用的是1MB的磁盘空间，而不是128MB。

==解决存储小文件办法子一==：HDFS存档文件或HAR文件，是一个更高效的文件存档工具，它将文件存入HDFS块，在减少NameNode内存使用的同时，允许对文件进行透明的访问。具体来说，HDFS存档文件对内还是一个一个独立文件，对NameNode而言却是一个整体，减少了NameNode的内存。
![[700 Attachments/Pasted image 20220119223547.png]]

### 实操
1. 启动yarn进程：`start-yarn.sh`
2. 把`user/lizhen/input`目录里面所有文件归档成一个叫input.har的归档文件，并把归档后文件存储到`user/lizhen/output`路径下。
	```java
	bin/hadoop archive -archiveName input.har –p  /user/lizhen/input   /user/lizhen/output
	```
3. 查看文档：`hadoop fs -lsr `
4. 解归档文件:
	```java
	hadoop fs -cp har:/// user/lizhen/output/input.har/*    /user/lizhen
	```

## 回收站
开启回收站功能，可以将删除的文件在不超时的情况下，恢复原数据，起到防止误删除、备份等作用。

### 回收站功能参数说明
1. 默认值`fs.trash.interval=0`,0表示禁用回收站；其他值表示设置文件的存活时间
2. 默认值`fs.trash.checkpoint.interval=0`，检查回收站的间隔时间。如果该值为0，则该值设置和`fs.trash.interval`的参数值相等。
3. 要求`fs.trash.checkpoint.interval<=fs.trash.interval`

### 回收站工作机制
![[700 Attachments/Pasted image 20220119224336.png]]

### 回收站实操
1. 启用回收站：修改core-site.xml，配置垃圾回收时间为1分钟。
	```xml
	<property>
		<name>fs.trash.interval</name>
		<value>1</value>
	</property>
	```
2. 查看回收站：回收站在集群中的路径：/user/lizhen/.Trash/….
3. 修改访问垃圾回收站用户名称：进入垃圾回收站用户名称，默认是dr.who，修改为lizhen用户，core-site.xml
	```xml
	<property>
	  <name>hadoop.http.staticuser.user</name>
	  <value>atguigu</value>
	</property>

	```
4. 通过程序删除的文件不会经过回收站，需要调用moveToTrash()才进入回收站
	```java
	Trash trash = New Trash(conf);
	trash.moveToTrash(path);
	```
	
5. 恢复回收站数据
	```bash
	hadoop fs -mv /user/atguigu/.Trash/Current/user/lizhen/input    /user/lizhen/input
	```

6. 清空回收站
	```bash
	hadoop fs -expunge
	```
	
## 多NN的HA架构
HDFS NameNode高可用性的初始实现为单个活动NameNode和单个备用NameNode，将edits复制到三个JournalNode。该体系结构能够容忍系统中一个NN或一个JN的故障。

但是，某些部署需要更高程度的容错能力。Hadoop3.x允许用户运行多个备用NameNode。例如，通过配置三个NameNode和五个JournalNode，群集能够容忍两个节点而不是一个节点的故障。


## 纠删码
HDFS中的默认3副本方案在存储空间和其他资源（例如，网络带宽）中具有200％的开销。但是，对于I / O活动相对较低暖和冷数据集，在正常操作期间很少访问其他块副本，但仍会消耗与第一个副本相同的资源量。

纠删码（Erasure Coding）能够在不到50% 的数据冗余情况下提供和3副本相同的容错能力，因此，使用纠删码作为副本机制的改进是自然而然的。

