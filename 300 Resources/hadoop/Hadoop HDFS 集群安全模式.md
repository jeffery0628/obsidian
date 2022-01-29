---
Create: 2022年 一月 16日, 星期日 16:27
tags: 
  - Engineering/hadoop
  - 大数据
---

## 集群安全模式
### NameNode启动
NameNode启动时，首先将镜像文件(Fsimage)载入内存，并执行编辑日志（Edits）中的各项操作。一旦在内存中成功建立文件系统元数据的映像，则创建一个新的Fsimage文件和一个空的编辑日志。此时，NameNode开始监听DataNode请求。这个过程期间，NameNode一直运行在安全模式，即NameNode的文件系统对于客户端来说是==只读==。

### DataNode 启动
系统中的数据块的位置并不是由NameNode维护的，而是以块列表的形式存储在DataNode中。在系统的正常操作期间，NameNode会在内存中保留所有块位置的映射信息。在安全模式下，各个DataNode会向NameNode发送最新的块列表信息，NameNode了解到足够多的块位置信息之后，即可高效运行文件系统。

### 安全模式退出判断
如果满足“最小副本条件”，NameNode会在30s之后就退出安全模式。所谓的最小副本条件指的是在整个文件系统中99.9%的块满足最小副本级别（dfs.replication.min=1）。在启动一个刚刚格式化的HDFS集群时，因为系统中还没有任何块，所以NameNode不会进入安全模式。

## 操作
### 基本语法
```bash
bin/hdfs dfsadmin -safemode get		（功能描述：查看安全模式状态）
bin/hdfs dfsadmin -safemode enter  	（功能描述：进入安全模式状态）
bin/hdfs dfsadmin -safemode leave	（功能描述：离开安全模式状态）
bin/hdfs dfsadmin -safemode wait	（功能描述：等待安全模式状态）
```

