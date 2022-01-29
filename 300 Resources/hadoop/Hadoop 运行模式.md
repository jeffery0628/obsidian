---
Create: 2022年 一月 1日, 星期六 20:56
tags: 
  - Engineering/hadoop
  - 大数据
---
[hadoop官网](http://hadoop.apache.org/)

Hadoop运行模式包括：
1. 本地模式
2. 伪分布式模式
3. 完全分布式模式。


## 本地运行模式
1. 在hadoop-3.1.3文件夹下创建一个wcinput文件夹
	```bash
	mkdir wcinput
	```
2. 在wcinput文件下创建一个wc.input文件
	```bash
	cd wcinput
	vim wc.input
	```
3. 编辑wc.input：
	```bash
	hadoop yarn
	hadoop mapreduce
	hallo world
	nihao china
	```

4. 回到目录：/opt/module/hadoop-3.1.3
	```bash
	cd /opt/module/hadoop-3.1.3
	```
5. 执行程序
	```bash
	hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount wcinput wcoutput
	```
6. 查看结果
	```bash
	cat wcoutput/part-r-00000

	china	1
	hadoop	2
	hallo	1
	mapreduce	1
	nihao	1
	world	1
	yarn	1
	```

## 完全分布式运行模式

![[300 Resources/hadoop/docker 搭建hadoop集群]]

### 集群配置

集群规划：

| 服务 | hadoop01                      | hadoop02                      | hadoop03    | hadoop04    | hadoop05    |
| ---- | ----------------------------- | ----------------------------- | ----------- | ----------- | ----------- |
| HDFS | NameNode \ DataNode           | SecondaryNameNode \ DataNode  | DataNode    | DataNode    | DataNode    |
| YARN | resourcemanager \ nodemanager | resourcemanager \ nodemanager | nodemanager | nodemanager | nodemanager |


核心配置文件(5个节点均进行此操作)：

进入/root/hadoop/etc/hadoop目录
```bash
cd /root/hadoop/etc/hadoop
```

在hadoop-env.sh,mapred-env.sh,yarn-env.sh中加入JAVA_HOME
```bash
export JAVA_HOME=/root/jdk8
```

==core-site.xml==
```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://ns</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/root/hadoop/tmp</value>
	</property>

	<property>
		<name>ha.zookeeper.quorum</name>
		<value>hadoop01:2181,hadoop02:2181,hadoop03:2181,hadoop04:2181,hadoop05:2181</value>
	</property>

</configuration>

```

==hdfs-site.xml==
```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
	<property>
		<name>dfs.replication</name>
		<value>3</value>
	</property>
	<property>
		<name>dfs.nameservices</name>
		<value>ns</value>
	</property>
	<property>
		<name>dfs.ha.namenodes.ns</name>
		<value>nn1,nn2</value>
	</property>
	<property>
		<name>dfs.namenode.rpc-address.ns.nn1</name>
		<value>hadoop01:9000</value>
	</property>
	<property>
		<name>dfs.namenode.http-address.ns.nn1</name>
		<value>hadoop01:50070</value>
	</property>
	<property>
		<name>dfs.namenode.rpc-address.ns.nn2</name>
		<value>hadoop02:9000</value>
	</property>
	<property>
		<name>dfs.namenode.http-address.ns.nns</name>
		<value>hadoop02:50070</value>
	</property>
	<property>
		<name>dfs.namenode.shared.edits.dir</name>
		<value>qjournal://hadoop01:8485;hadoop02:8485;hadoop03:8485;hadoop04:8485;hadoop05:8485/ns</value>
	</property>
	<property>
		<name>dfs.journalnode.edits.dir</name>
		<value>/root/hadoop/journal/data</value>
	</property>
	<property>
		<name>dfs.ha.automatic-failover.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>dfs.client.failover.proxy.provider.ns</name>
		<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
	</property>
	<property>
		<name>dfs.ha.fencing.methods</name>
		<value>
			sshfence
			shell(/bin/true)
		</value>
	</property>
	<property>
		<name>dfs.ha.fencing.ssh.private-key-files</name>
		<value>/root/.ssh/id_rsa</value>
	</property>
	<property>
		<name>dfs.ha.fencing.ssh.connect-timeout</name>
		<value>30000</value>
	</property>
</configuration>


```

==mapred-site.xml==
```xml
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->

<!-- Put site-specific property overrides in this file. -->

<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <property>
                <name>yarn.app.mapreduce.am.env</name>
                <value>HADOOP_MAPRED_HOME=/root/hadoop/</value>
        </property>
        <property>
                <name>mapreduce.map.env</name>
                <value>HADOOP_MAPRED_HOME=/root/hadoop/</value>
        </property>
        <property>
                <name>mapreduce.reduce.env</name>
                <value>HADOOP_MAPRED_HOME=/root/hadoop/</value>
        </property>
</configuration>


```

==yarn-site.xml==

```xml
<?xml version="1.0"?>
<!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
-->
<configuration>
	<property>
		<name>yarn.resourcemanager.ha.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>yarn.resourcemanager.cluster-id</name>
		<value>yrc</value>
	</property>
	<property>
		<name>yarn.resourcemanager.ha.rm-ids</name>
		<value>rm1,rm2</value>
	</property>
	<property>
		<name>yarn.resourcemanager.hostname.rm1</name>
		<value>hadoop01</value>
	</property>
	<property>
		<name>yarn.resourcemanager.hostname.rm2</name>
		<value>hadoop02</value>
	</property>
	<property>
		<name>yarn.resourcemanager.zk-address</name>
		<value>hadoop01:2181,hadoop02:2181,hadoop03:2181,hadoop04:2181,hadoop05:2181</value>
	</property>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
		<property>
        <name>yarn.resourcemanager.webapp.address.rm1</name>
        <value>hadoop01:8088</value>
    </property>
    <property>
        <name>yarn.resourcemanager.scheduler.address.rm2</name>
        <value>hadoop02:8088</value>
    </property>
    <property>
        <name>yarn.resourcemanager.webapp.address.rm2</name>
        <value>hadoop02:8088</value>
    </property>
<!-- Site specific YARN configuration properties -->

</configuration>


```

==workers==
```
hadoop01
hadoop02
hadoop03
hadoop04
hadoop05
```

### 同步到其他集群
```bash
sudo xsync /root/hadoop/etc/hadoop/
```

## 启动集群
启动JournalNode(==5个节点均要执行==)：
```
hadoop-daemon.sh (start/stop) journalnode
```

在hadoop01节点上执行命令:
1. 格式化NameNode：
	```bash
	hdfs namenode -format
	```
2. 进入/root/hadoop，将tmp文件夹远程复制到hadoop02的/root/hadoop：
	```bash
	cd /root/hadoop
	scp -r tmp/ root@hadoop02:/root/hadoop
	```
	
3. 格式化ZKCF：
	```bash
	hdfs zkfc -formatZK
	```
	
4. 修改start-dfs.sh：
	```bash
	cd /root/hadoop/sbin
	vim start-dfs.sh
	增加以下内容
	
	HDFS_NAMENODE_USER=root
	HDFS_DATANODE_USER=root
	HDFS_JOURNALNODE_USER=root
	HDFS_ZKFC_USER=root
	```
	
5. 修改start-yarn.sh:
	```bash
	cd /root/hadoop/sbin
	vim start-yarn.sh
	增加以下内容
	YARN_RESOURCEMANAGER_USER=root
	YARN_NODEMANAGER_USER=root
	```
	
6. 启动hdfs
	```bash
	start-dfs.sh
	```
	
7. 启动yarn：
	```bash
	start-yarn.sh
	```