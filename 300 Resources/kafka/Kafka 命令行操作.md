---
Create: 2022年 四月 16日, 星期六 20:15
tags: 
  - Engineering/kafka
  - 大数据
---

1. 查看当前服务器中的所有topic:
	```
	bin/kafka-topics.sh --zookeeper hadoop102:2181/kafka --list
	```
2. 创建topic:
	```
	bin/kafka-topics.sh --zookeeper hadoop102:2181/kafka --create --replication-factor 3 --partitions 1 --topic first
	```
	选项说明：
	--topic 定义topic名
	--replication-factor  定义副本数
	--partitions  定义分区数
3. 删除topic
	```
	bin/kafka-topics.sh --zookeeper hadoop102:2181/kafka --delete --topic first
	```
	需要server.properties中设置delete.topic.enable=true否则只是标记删除。
4. 发送消息
	```
	bin/kafka-console-producer.sh --broker-list hadoop102:9092 --topic first
	
	hello 
	world
	``` 

5. 消费消息
	```
	bin/kafka-console-consumer.sh --bootstrap-server hadoop102:9092 --from-beginning --topic first

	```
	--from-beginning：会把主题中以往所有的数据都读取出来。
6. 查看某个Topic的详情
	```
	bin/kafka-topics.sh --zookeeper hadoop102:2181/kafka --describe --topic first
	```
1. 修改分区数
	```
	bin/kafka-topics.sh --zookeeper hadoop102:2181/kafka --alter --topic first --partitions 6
	```



