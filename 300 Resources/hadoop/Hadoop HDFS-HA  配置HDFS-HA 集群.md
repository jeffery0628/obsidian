---
Create: 2022年 一月 20日, 星期四 10:16
tags: 
  - Engineering/hadoop
  - 大数据
---

## 配置HDFS-HA 集群

1. [官网地址](http://hadoop.apache.org/)
2. 在opt目录下创建一个ha文件夹
	```bash
	sudo mkdir ha
	sudo chown lizhen:lizhen /opt/ha
	```
	
3. 将/opt/module/下的 hadoop-3.1.3拷贝到/opt/ha目录下
	```bash
	cp -r /opt/module/hadoop-3.1.3 /opt/ha/
	```
4. 配置hadoop-env.sh
	```bash
	export JAVA_HOME=/opt/module/jdk1.8.0_144
	```
	
5. 配置core-site.xml
	```xml
	<configuration>
	  <property>
		<name>fs.defaultFS</name>
		<value>hdfs://mycluster</value>
	  </property>
	  <property>
		<name>hadoop.data.dir</name>
		<value>/opt/ha/hadoop-3.1.3/data</value>
	  </property>
	</configuration>

	```
	
6. 配置hdfs-site.xml
	```xml
	<configuration>
	  <property>
		<name>dfs.namenode.name.dir</name>
		<value>file://${hadoop.data.dir}/name</value>
	  </property>
	  <property>
		<name>dfs.datanode.data.dir</name>
		<value>file://${hadoop.data.dir}/data</value>
	  </property>
	  <property>
		<name>dfs.nameservices</name>
		<value>mycluster</value>
	  </property>
	  <property>
		<name>dfs.ha.namenodes.mycluster</name>
		<value>nn1,nn2, nn3</value>
	  </property>
	  <property>
		<name>dfs.namenode.rpc-address.mycluster.nn1</name>
		<value>hadoop102:8020</value>
	  </property>
	  <property>
		<name>dfs.namenode.rpc-address.mycluster.nn2</name>
		<value>hadoop103:8020</value>
	  </property>
	  <property>
		<name>dfs.namenode.rpc-address.mycluster.nn3</name>
		<value>hadoop104:8020</value>
	  </property>
	  <property>
		<name>dfs.namenode.http-address.mycluster.nn1</name>
		<value>hadoop102:9870</value>
	  </property>
	  <property>
		<name>dfs.namenode.http-address.mycluster.nn2</name>
		<value>hadoop103:9870</value>
	  </property>
	  <property>
		<name>dfs.namenode.http-address.mycluster.nn3</name>
		<value>hadoop104:9870</value>
	  </property>
	  <property>
		<name>dfs.namenode.shared.edits.dir</name>
		<value>qjournal://hadoop102:8485;hadoop103:8485;hadoop104:8485/mycluster</value>
	  </property>
	  <property>
		<name>dfs.client.failover.proxy.provider.mycluster</name>
		<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
	  </property>
	  <property>
		<name>dfs.ha.fencing.methods</name>
		<value>sshfence</value>
	  </property>
	  <property>
		<name>dfs.ha.fencing.ssh.private-key-files</name>
		<value>/home/atguigu/.ssh/id_ecdsa</value>
	  </property>
	  <property>
		<name>dfs.journalnode.edits.dir</name>
		<value>${hadoop.data.dir}/jn</value>
	  </property>
	</configuration>

	```
7. 拷贝配置好的hadoop环境到其他节点




