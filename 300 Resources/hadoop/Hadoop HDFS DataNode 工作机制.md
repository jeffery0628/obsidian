---
Create: 2022年 一月 16日, 星期日 16:37
tags: 
  - Engineering/hadoop
  - 大数据
---

## DataNode 工作机制
![[700 Attachments/Pasted image 20220116164504.png]]

1. 一个数据块在DataNode上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。
2. DataNode启动后向NameNode注册，通过后，周期性（1小时）的向NameNode上报所有的块信息。
3. 心跳是每3秒一次，心跳返回结果带有NameNode给该DataNode的命令如复制块数据到另一台机器，或删除某个数据块。如果超过10分钟没有收到某个DataNode的心跳，则认为该节点不可用。
4. 集群运行中可以安全加入和退出一些机器


## 数据完整性
==思考==：如果电脑磁盘里面存储的数据是控制高铁信号灯的 红灯信号1 和 绿灯信号0 ，但是存储该数据的磁盘坏了，一直显示是绿灯，是否很危险？同理DataNode节点上的数据损坏了，却没有发现，是否也很危险，那么如何解决呢？

如下是DataNode节点保证数据完整性的方法。
1. 当DataNode读取Block的时候，它会计算CheckSum。
2. 如果计算后的CheckSum，与Block创建时值不一样，说明Block已经损坏。
3. Client读取其他DataNode上的Block。
4. DataNode在其文件创建后周期验证CheckSum。

![[700 Attachments/Pasted image 20220116164807.png]]

### 掉线时限参数设置
1. DataNode进程死亡或者网络故障造成DataNode无法与NameNode通信
2. NameNode不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。
3. HDFS默认的超时时长为10分钟+30秒。
4. 如果定义超时时间为TimeOut，则超时时长的计算公式为：`TimeOut = 2 * dfs.namenode.heartbeat.recheck-interval + 10 * dfs.heartbeat.interval`而默认的`dfs.namenode.heartbeat.recheck-interval`大小为5分钟，`dfs.heartbeat.interval`默认为3秒。
	> 需要注意的是hdfs-site.xml 配置文件中的heartbeat.recheck.interval的单位为==毫秒==，dfs.heartbeat.interval的单位为==秒==。

