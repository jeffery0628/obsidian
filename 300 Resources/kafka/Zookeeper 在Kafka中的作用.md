---
Create: 2022年 四月 16日, 星期六 16:36
tags: 
  - Engineering/kafka
  - 大数据
---

Kafka集群中有一个broker会被选举为Controller，负责管理集群broker的上下线，所有topic的分区副本分配和leader选举等工作。
Controller的管理工作都是依赖于Zookeeper的。
以下为partition的leader选举过程：
![[700 Attachments/Pasted image 20220416230527.png]]
![[700 Attachments/Pasted image 20220416230547.png]]
![[700 Attachments/Pasted image 20220416230655.png]]
![[700 Attachments/Pasted image 20220416230605.png]]
![[700 Attachments/Pasted image 20220416230627.png]]




