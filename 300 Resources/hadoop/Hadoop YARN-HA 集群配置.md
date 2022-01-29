---
Create: 2022年 一月 20日, 星期四 10:19
tags: 
  - Engineering/hadoop
  - 大数据
---

## YARN-HA 集群配置

### 环境准备
	1. 修改IP
	2. 修改主机名及主机名和IP地址的映射
	3. 关闭防火墙
	4. ssh免密登录
	5. 安装JDK，配置环境变量等
	6. 配置Zookeeper集群


### 规划集群

| hadoop101       | hadoop102       | hadoop103   |
| --------------- | --------------- | ----------- |
| NameNode        | NameNode        |             |
| JournalNode     | JournalNode     | JournalNode |
| DataNode        | DataNode        | DataNode    |
| ZK              | ZK              | ZK          |
| ResourceManager | ResourceManager |             |
| NodeManager     | NodeManager     | NodeManager |

### 具体配置
1. yarn-site.xml
	```xml
	<configuration>

		<property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
		</property>

		<!--启用resourcemanager ha-->
		<property>
			<name>yarn.resourcemanager.ha.enabled</name>
			<value>true</value>
		</property>

		<!--声明两台resourcemanager的地址-->
		<property>
			<name>yarn.resourcemanager.cluster-id</name>
			<value>cluster-yarn1</value>
		</property>

		<property>
			<name>yarn.resourcemanager.ha.rm-ids</name>
			<value>rm1,rm2</value>
		</property>

		<property>
			<name>yarn.resourcemanager.hostname.rm1</name>
			<value>hadoop102</value>
		</property>

		<property>
			<name>yarn.resourcemanager.hostname.rm2</name>
			<value>hadoop103</value>
		</property>

		<!--指定zookeeper集群的地址--> 
		<property>
			<name>yarn.resourcemanager.zk-address</name>
			<value>hadoop102:2181,hadoop103:2181,hadoop104:2181</value>
		</property>

		<!--启用自动恢复--> 
		<property>
			<name>yarn.resourcemanager.recovery.enabled</name>
			<value>true</value>
		</property>

		<!--指定resourcemanager的状态信息存储在zookeeper集群--> 
		<property>
			<name>yarn.resourcemanager.store.class</name>     <value>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore</value>
	</property>

	</configuration>

	```

2. 同步更新其他节点的配置信息

### 启动hdfs
1. 在各个JournalNode节点上，输入以下命令启动journalnode服务
	```bash
	hdfs --daemon start journalnode
    ```
2. 在\[nn1\]上，对其进行格式化，并启动：
	```bash
	hdfs namenode -format
	hdfs --daemon start namenode
	```

3. 在\[nn2\]上，同步nn1的元数据信息：
	```bash
	hdfs namenode -bootstrapStandby
	```
4. 启动\[nn2\]：
	```bash
	hdfs --daemon start namenode
	```
5. 启动所有DataNode
	```bash
	hdfs –-daemon start datanode
	```
6. 将\[nn1\]切换为Active
	```bash
	hdfs haadmin -transitionToActive nn1
	```