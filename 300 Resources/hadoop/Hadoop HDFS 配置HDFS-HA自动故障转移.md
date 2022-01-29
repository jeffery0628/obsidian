---
Create: 2022年 一月 20日, 星期四 10:19
tags: 
  - Engineering/hadoop
  - 大数据
---

## 配置HDFS-HA自动故障转移
### 配置
1. 在hdfs-site.xml中增加
	```xml
	<property>
		<name>dfs.ha.automatic-failover.enabled</name>
		<value>true</value>
	</property>
	```

2. 在core-site.xml文件中增加
```xml
<property>
	<name>ha.zookeeper.quorum</name>
	<value>hadoop102:2181,hadoop103:2181,hadoop104:2181</value>
</property>
```



### 启动
1. 关闭所有HDFS服务：stop-dfs.sh
2. 启动Zookeeper集群：zkServer.sh start
3. 初始化HA在Zookeeper中状态：hdfs zkfc -formatZK
4. 启动HDFS服务：start-dfs.sh


### 验证
1. 将Active NameNode进程kill : kill -9 namenode的进程id
2. 将Active NameNode机器断开网络: service network stop





