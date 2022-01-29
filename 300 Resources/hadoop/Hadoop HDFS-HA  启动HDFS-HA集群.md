---
Create: 2022年 一月 20日, 星期四 10:19
tags: 
  - Engineering/hadoop
  - 大数据
---


## 启动HDFS-HA集群
1. 将HADOOP_HOME环境变量更改到HA目录
	```bash
	sudo vim /etc/profile.d/my_env.sh
	```
	将HADOOP_HOME部分改为如下
	```bash
	##HADOOP_HOME
	export HADOOP_HOME=/opt/ha/hadoop-3.1.3
	export PATH=$PATH:$HADOOP_HOME/bin
	export PATH=$PATH:$HADOOP_HOME/sbin
	```

2. 在各个JournalNode节点上，输入以下命令启动journalnode服务
	```bash
	hdfs --daemon start journalnode
	```
3. 在\[nn1\]上，对其进行格式化，并启动
	```bash
	hdfs namenode -format
	hdfs --daemon start namenode
	```
4. 在\[nn2\]和\[nn3\]上，同步nn1的元数据信息
	```bash
	hdfs namenode -bootstrapStandby
	```
5. 启动\[nn2\]和\[nn3\]
	```bash
	hdfs --daemon start namenode
	```

6. 查看web页面显示 
	![[700 Attachments/Pasted image 20220120100921.png]]

7. 在所有节点上上，启动datanode
	```bash
	hdfs --daemon start datanode
	```
8. 将\[nn1\]切换为Active
	```bash
	hdfs haadmin -transitionToActive nn1
	```

9. 查看是否Active
	```bash
	hdfs haadmin -getServiceState nn1
	```



