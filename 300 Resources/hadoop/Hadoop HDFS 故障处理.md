---
Create: 2022年 一月 16日, 星期日 16:20
tags: 
  - Engineering/hadoop
  - 大数据
---
NameNode故障后，可以采用如下两种方法恢复数据：
1. 将SecondaryNameNode中数据拷贝到NameNode存储数据的目录；
	1. kill -9 NameNode进程
	2. 删除NameNode存储的数据 `rm `
	3. 拷贝SecondaryNameNode中数据到原NameNode存储数据目录 `scp`
	4. 重新启动NameNode  ： `hdfs --daemon start namenode`

2. 使用-importCheckpoint选项启动NameNode守护进程，从而将SecondaryNameNode中数据拷贝到NameNode目录中。
	1. 修改hdfs-site.xml中:
		```xml
		<property>
    		<name>dfs.namenode.checkpoint.period</name>
			<value>120</value>
		</property>

		<property>
			<name>dfs.namenode.name.dir</name>
			<value>/opt/module/hadoop-3.1.3/data/tmp/dfs/name</value>
		</property>

		```
	2. kill -9 NameNode进程
	3. 删除NameNode存储的数据
	4. 如果SecondaryNameNode不和NameNode在一个主机节点上，需要将SecondaryNameNode存储数据的目录拷贝到NameNode存储数据的同级目录，并删除in_use.lock文件
	5. 导入检查点数据 `bin/hdfs namenode -importCheckpoint`(等待一会ctrl+c结束掉)
	6. 启动NameNode


