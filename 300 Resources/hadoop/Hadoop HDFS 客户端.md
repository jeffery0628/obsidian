---
Create: 2022年 一月 16日, 星期日 13:12
tags: 
  - Engineering/hadoop
  - 大数据
---
## HDFS 写数据流程
![[700 Attachments/Pasted image 20220116152918.png]]

1. 客户端通过Distributed FileSystem模块向NameNode请求上传文件，NameNode检查目标文件是否已存在，父目录是否存在。
2. NameNode返回是否可以上传。
3. 客户端请求第一个 Block上传到哪几个DataNode服务器上。
4. NameNode返回3个DataNode节点，分别为dn1、dn2、dn3。
5. 客户端通过FSDataOutputStream模块请求dn1上传数据，dn1收到请求会继续调用dn2，然后dn2调用dn3，将这个通信管道建立完成。
6. dn1、dn2、dn3逐级应答客户端。
7. 客户端开始往dn1上传第一个Block（先从磁盘读取数据放到一个本地内存缓存），以Packet为单位，dn1收到一个Packet就会传给dn2，dn2传给dn3；dn1每传一个packet会放入一个应答队列等待应答。
8. 当一个Block传输完成之后，客户端再次请求NameNode上传第二个Block的服务器。（重复执行3-7步）。


### 网络拓扑-节点距离计算
在HDFS写数据的过程中，NameNode会选择距离待上传数据最近距离的DataNode接收数据。那么这个最近距离怎么计算呢？
==节点距离==：两个节点到达最近的共同祖先的距离总和。

例如，假设有数据中心d1机架r1中的节点n1。该节点可以表示为/d1/r1/n1。利用这种标记，这里给出四种距离描述。
![[700 Attachments/Pasted image 20220116151000.png]]
D1，R1 都是交换机，最底层是 datanode。则 H1 的 rackid=/D1/R1/H1，H1 的 parent 是 R1，R1 的是 D1。这些 rackid信息可以通过 topology.script.file.name 配置。有了这些 rackid 信息就可以计算出任意两台 datanode 之间的距离。

1. distance(/D1/R1/H1,/D1/R1/H1)=0 相同的 datanode
2. distance(/D1/R1/H1,/D1/R1/H2)=2 同一 rack 下的不同 datanode
3. distance(/D1/R1/H1,/D1/R1/H4)=4 同一 IDC（互联网数据中心、机房）下的不同 datanode
4. distance(/D1/R1/H1,/D2/R3/H7)=6 不同 IDC 下的 datanode


### 机架感知
客户端向 Namenode 发送写请求时，Namenode 为这些数据分配 Datanode 地址，HDFS 数据块副本的放置对于系统整体的可靠性和性能有关键性影响。一个简单但非优化的副本放置策略是，把副本分别放在不同机架，甚至不同IDC，这样可以防止整个机架，甚至整个IDC崩溃带来的数据丢失。

### 副本节点的选择
![[700 Attachments/Pasted image 20220116151554.png]]

1. 第一个数据副本在Client所处的节点上，如果客户端在集群外，随机选一个；
2. 第二个数据副本和第一个数据副本位于相同的机架，随机节点；
3. 第三个数据副本位于不同机架，随机节点

## HDFS 读取数据流程
![[700 Attachments/Pasted image 20220116152938.png]]
1. 客户端通过Distributed FileSystem向NameNode请求下载文件，NameNode通过查询元数据，找到文件块所在的DataNode地址。
2. 挑选一台DataNode（就近原则，然后随机）服务器，请求读取数据。
3. DataNode开始传输数据给客户端（从磁盘里面读取数据输入流，以Packet为单位来做校验）。
4. 客户端以Packet为单位接收，先在本地缓存，然后写入目标文件。
