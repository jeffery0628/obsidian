---
Create: 2022年 一月 20日, 星期四 13:22
tags: 
  - Engineering/zookeeper
  - 大数据
---
## 概述
Zookeeper是一个开源的分布式的，为分布式应用提供协调服务的Apache项目。

![[700 Attachments/Pasted image 20220120132736.png]]

## 特点
![[700 Attachments/Pasted image 20220120132755.png]]
1. ZooKeeper ： 一个领导者(Leader)，多个跟随者(Follower)组成的集群。
2. 集群中只要有半数以上节点存活，Zookeeper集群就能正常服务。
3. 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。
4. 更新请求顺序进行，来自同一个Client的更新请求按其发送顺序依次执行。
5. 数据更新原子性，一次数据更新要么成功，要么失败。
6. 实时性，在一定时间范围内，Client能读取到最新数据。

## 数据结构
Zookeeper数据模型的结构与Unix文件系统很类似，整体上可以看作是一棵树，每个节点称作一个ZNode。每个ZNode默认能够存储1MB的数据，每个ZNode都可以通过其路径唯一标识。

![[700 Attachments/Pasted image 20220120133219.png]]




## 应用场景
提供的服务包括：
1. 统一命名服务
2. 统一配置管理
3. 统一集群管理
4. 服务器节点动态上下线
5. 软负载均衡
6. 等。

### 统一配置管理
![[700 Attachments/Pasted image 20220120133824.png]]
1. 分布式环境下，配置文件同步非常常见。
	1. 一般要求一个集群中，所有节点的配置信息是一致的，比如Kafka集群。
	2. 对配置文件修改后，希望能够快速同步到各个节点上。

2. 配置管理可交由Zookeeper实现。
	1. 可将配置信息写入Zookeeper上的一个ZNode
	2. 各个客户端服务器监听这个ZNode
	3. 一旦ZNode中的数据被修改，Zookeeper将通知各个客户端服务器。


### 统一集群管理
![[700 Attachments/Pasted image 20220120133849.png]]
1. 分布式环境中，实时掌握每个节点的状态是必要的，可根据节点实时状态做出一些调整。
2. Zookeeper可以实现实时监控节点状态变化
	1. 可将节点信息写入Zookeeper上的一个ZNode
	2. 监听这个ZNode可获取它的实时状态变化。


### 服务器动态上下线
![[700 Attachments/Pasted image 20220120133911.png]]

### 软负载均衡
在Zookeeper中记录每台服务器的访问数，让访问数最少的服务器去处理最新的客户端请求
![[700 Attachments/Pasted image 20220120134022.png]]


## 下载地址
[官网地址](https://zookeeper.apache.org)
![[700 Attachments/Pasted image 20220120134106.png]]
![[700 Attachments/Pasted image 20220120134117.png]]
![[700 Attachments/Pasted image 20220120134127.png]]