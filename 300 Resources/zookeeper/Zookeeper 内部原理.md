---
Create: 2022年 一月 21日, 星期五 09:23
tags: 
  - Engineering/zookeeper
  - 大数据
---

## 节点类型
==持久==：客户端和服务器端断开连接后，创建的节点不删除
==短暂==：客户端和服务器端断开连接后，创建的节点自己删除
![[700 Attachments/Pasted image 20220121092911.png]]

> 说明：创建znode时，设置顺序标识，znode名称后会附加一个值，顺序号是一个单调递增的计数器，由父节点维护
> 在分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序。

1. 持久化目录节点：客户端与Zookeeper断开连接后，该节点依旧存在
2. 持久化顺序编号目录节点：客户端与Zookeeper断开连接后，该节点依旧存在，知识Zookeeper给该节点名称进行顺序编号
3. 临时目录节点：客户端与Zookeeper断开连接后，该节点被删除。
4. 临时顺序编号目录节点：客户端与Zookeeper断开连接后，该节点被删除，只是Zookeeper给该节点名称进行顺序编号。


## Stat 结构体
1. czxid-创建节点的事务zxid。每次修改ZooKeeper状态都会收到一个zxid形式的时间戳，也就是ZooKeeper事务ID。事务ID是ZooKeeper中所有修改总的次序。每个修改都有唯一的zxid，如果zxid1小于zxid2，那么zxid1在zxid2之前发生。
2. ctime - znode被创建的毫秒数(从1970年开始)
3. mzxid - znode最后更新的事务zxid
4. mtime - znode最后修改的毫秒数(从1970年开始)
5. pZxid-znode最后更新的子节点zxid
6. cversion - znode子节点变化号，znode子节点修改次数
7. dataversion - znode数据变化号
8. aclVersion - znode访问控制列表的变化号
9. ephemeralOwner- 如果是临时节点，这个是znode拥有者的session id。如果不是临时节点则是0。
10. dataLength- znode的数据长度
11. numChildren - znode子节点数量



## 监听器原理
### 原理详解
1. 首先要有一个main()线程
2. 在main线程中创建Zookeeper客户端，这时就会创建两个线程，一个负责网络连接通信（connect），一个负责监听（listener）
3. 通过connect线程将注册的监听事件发送给Zookeeper。
4. 在Zookeeper的注册监听器列表中，将注册的监听事件添加到列表中。
5. Zookeeper监听到有数据或路径变化，就会将这个消息发送给listener线程。
6. listener 线程内部调用了process()方法。

### 常见的监听
1. 监听节点数据的变化：get path \[watch\]
2. 监听子节点增减的变化：ls path \[watch\]

![[700 Attachments/Pasted image 20220121094451.png]]
## Paxos 算法
Paxos算法一种基于消息传递且具有高度容错特性的一致性算法。
分布式系统中的节点通信存在两种模型：共享内存（Shared memory）和消息传递（Messages passing）。基于消息传递通信模型的分布式系统，不可避免的会发生以下错误：进程可能会慢、被杀死或者重启，消息可能会延迟、丢失、重复，在基础 Paxos 场景中，先不考虑可能出现消息篡改即拜占庭错误的情况。Paxos 算法解决的问题是在一个可能发生上述异常的分布式系统中如何就某个值达成一致，保证不论发生以上任何异常，都不会破坏决议的一致性。

### 算法描述
 在一个Paxos系统中，首先将所有节点划分为Proposers，Acceptors 和 Learners(每个节点都可以身兼数职)
![[700 Attachments/Pasted image 20220121094702.png]]

一个完整的Paxos算法流程分为三个阶段：
1. Prepare阶段
	1. Propose向Acceptors发出Prepare请求Promise（承诺）
	2. Acceptors 针对收到的Prepare请求进行Promise承诺
2. Accept 阶段
	1. Proposer收到多个Acceptors承诺的Promise后，向Acceptors发出Propose请求
	2. Acceptors针对收到的Propose请求进行Accept处理
3. Learn阶段：Propose将形成的决议发送给所有Learners

Paxos算法流程中的每条消息描述如下：
1. Prepare: Proposer生成全局唯一且递增的Proposal ID (可使用时间戳加Server ID)，向所有Acceptors发送Prepare请求，这里无需携带提案内容，只携带Proposal ID即可。
2. Promise: Acceptors收到Prepare请求后，做出“两个承诺，一个应答”。
	两个承诺：
	1. 不再接受Proposal ID小于等于（注意：这里是<= ）当前请求的Prepare请求。
	2. 不再接受Proposal ID小于（注意：这里是< ）当前请求的Propose请求。

	一个应答：
	1. 不违背以前做出的承诺下，回复已经Accept过的提案中Proposal ID最大的那个提案的Value和Proposal ID，没有则返回空值。
3. Propose: Proposer 收到多数Acceptors的Promise应答后，从应答中选择Proposal ID最大的提案的Value，作为本次要发起的提案。如果所有应答的提案Value均为空值，则可以自己随意决定提案Value。然后携带当前Proposal ID，向所有Acceptors发送Propose请求。
4. Accept: Acceptor收到Propose请求后，在不违背自己之前做出的承诺下，接受并持久化当前Proposal ID和提案Value。
5. Learn: Proposer收到多数Acceptors的Accept后，决议形成，将形成的决议发送给所有Learners。

下面针对上述描述做三种情况的推演举例：为了简化流程，这里不设置Learner。

情况一：
有A1,A2,A3,A4,A5 5位议员，就税率entity进行决议
![[700 Attachments/Pasted image 20220121095246.png]]
1. A1发起1号Proposal的Prepare，等待承诺；
2. A2-A5回应Promise
3. A1在收到两份恢复时就会发起税率10%的Proposal；
4. A2-A5回应Accept
5. 通过Proposal，税率10%


情况二：
假设A1提出提案的同时，A5决定将税率定为20%
![[700 Attachments/Pasted image 20220121095511.png]]
1. A1,A5 同时发起Prepare（序号分别为1，2）
2. A2承诺A1，A4承诺A5，A3行为成为关键
3. 情况1：
	1. A3先收到A1消息，承诺A1
	2. A1发起Proposal（1，10%），A2,A3接受
	3. 之后A3又收到A5消息，回复：（10%，1），并承诺A5.
	4. A5发起Proposal（2，10%），A3，A4接受。
	5. A1，A5同时广播决议。
	
	情况2：
	1. A3先收到A1消息，承诺A1。之后立刻收到A5消息，承诺A5
	2. A1发起Proposal（1，10%），无足够响应，A1重新Prepare（序号3），A3再次承诺A1.
	3. A5发起Proposal（2，20%），无足够响应，A5重新Prepare（序号3），A3再次承诺A5.
	4. ...


> 情况1：Paxos算法缺陷：在网络复杂的情况下，一个应用Paxos算法的分布式系统，可能很久无法收敛，甚至陷入活锁的情况。
> 情况2：造成这种情况的原因是系统中有一个以上的Proposer，多个Proposers相互争夺Acceptors，造成迟迟无法达成一致的情况。针对这种情况，一种改进的Paxos算法被提出：从系统中选出一个节点作为Leader，只有Leader能够发起提案。这样，一次Paxos流程中只有一个Proposer，不会出现活锁的情况，此时只会出现例子中第一种情况。


## 选举机制
==半数机制==：集群中半数以上机器存活，集群可用。所以Zookeeper适合安装奇数台服务器。

Zookeeper虽然在配置文件中并没有指定Master和Slave。但是，Zookeeper工作时，是有一个节点为Leader，其他则为Follower，Leader是通过内部的选举机制临时产生的。

以一个简单的例子来说明整个选举的过程：假设有五台服务器组成的Zookeeper集群，它们的id从1-5，同时它们都是最新启动的，也就是没有历史数据，在存放数据量这一点上，都是一样的。假设这些服务器依序启动，来看看会发生什么。
![[700 Attachments/Pasted image 20220121100253.png]]

1. 服务器1启动，发起一次选举。服务器1投自己一票。此时服务器1票数一票，不够半数以上（3票），选举无法完成，服务器1状态保持为LOOKING；
2. 服务器2启动，再发起一次选举。服务器1和2分别投自己一票并交换选票信息：此时服务器1发现服务器2的ID比自己目前投票推举的（服务器1）大，更改选票为推举服务器2。此时服务器1票数0票，服务器2票数2票，没有半数以上结果，选举无法完成，服务器1，2状态保持LOOKING
3. 服务器3启动，发起一次选举。此时服务器1和2都会更改选票为服务器3。此次投票结果：服务器1为0票，服务器2为0票，服务器3为3票。此时服务器3的票数已经超过半数，服务器3当选Leader。服务器1，2更改状态为FOLLOWING，服务器3更改状态为LEADING；
4. 服务器4启动，发起一次选举。此时服务器1，2，3已经不是LOOKING状态，不会更改选票信息。交换选票信息结果：服务器3为3票，服务器4为1票。此时服务器4服从多数，更改选票信息为服务器3，并更改状态为FOLLOWING；
5. 服务器5启动，同4一样当小弟。






