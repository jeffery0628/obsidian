---
Create: 2022年 一月 16日, 星期日 11:22
tags: 
  - Engineering/hadoop
  - 大数据
---

## 背景
随着数据量越来越大，在一个操作系统存不下所有的数据，那么就分配到更多的操作系统管理的磁盘中，但是不方便管理和维护，迫切需要一种系统来管理多台机器上的文件，这就是分布式文件管理系统。==HDFS只是分布式文件管理系统中的一种==。

## 定义
HDFS(Hadoop Distributed File System)，它是一个文件系统，用于存储文件，通过目录树来定位文件，其次，它是分布式的，由很多服务器联合起来实现其功能，集群中的服务器有各自的角色。

## 使用场景
HDFS的使用场景：适合一次写入，多次读出的场景，且不支持文件的修改。适合用来做数据分析，并不适合用来做网盘应用。

## HDFS优缺点
### 优点
1. 高容错性
	1. 数据自动保存多个副本，通过增加副本的形式，提高容错性。
		![[700 Attachments/Pasted image 20220116112943.png]]
	2. 某一个副本丢失以后，它可以自动回复。
		![[700 Attachments/Pasted image 20220116113114.png]]
2. 适合处理大数据
	1. 数据规模：能够处理数据规模达到GB、TB甚至PB级别的数据;
	2. 文件规模：能够处理百万规模以上的文件数量，数量相当之大。
3. 可构建在廉价的机器上，通过多副本机制，提高可靠性。

### 缺点
1. 不适合低延时数据访问，比如毫秒级的存储数据，是做不到的。
2. 无法高效的对大量小文件进行存储。
	1. 存储大量小文件的话，他会占用NameNode大量的内存来存储文件目录和块信息。这样是不可取的，因为NameNode的内存总是有限的；
	2. 小文件存储的寻址时间会超出读取时间，它违反了HDFS的设计目标。
3. 不支持并发写入、文件随机修改
	1. 一个文件只能有一个写入，不允许多个线程同时写；
	2. 仅支持数据append，不支持文件的随机修改。


