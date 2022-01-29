---
Create: 2022年 一月 16日, 星期日 16:27
tags: 
  - Engineering/hadoop
  - 大数据
---

## 服役新数据节点
### 需求
随着公司业务的增长，数据量越来越大，原有的数据节点的容量已经不能满足存储数据的需求，需要在原有集群基础上动态添加新的数据节点。

### 步骤
1. 直接启动DataNode，即可关联到集群
	```bash
	hdfs --daemon start datanode
	sbin/yarn-daemon.sh start nodemanager
	```
2. 上传文件
	```bash
	hadoop fs -put /opt/module/hadoop-3.1.3/LICENSE.txt /
	```
3. 如果数据不均衡，可以用命令实现集群的再平衡
	```bash
	./start-balancer.sh
	```

## 退役就数据节点
### 添加白名单
添加到白名单的主机节点，都允许访问NameNode，不在白名单的主机节点，都会被退出。
配置白名单的具体步骤如下：
1. 在NameNode的/opt/module/hadoop-3.1.3/etc/hadoop目录下创建dfs.hosts文件,并添加主机名称
	```
	hadoop102
	hadoop103
	hadoop104
	```
2. 在NameNode的hdfs-site.xml配置文件中增加dfs.hosts属性
	```xml
	<property>
		<name>dfs.hosts</name>
		<value>/opt/module/hadoop-3.1.3/etc/hadoop/dfs.hosts</value>
	</property>
	```
3. 配置文件分发:`xsync hdfs-site.xml`
4. 刷新NameNode: `hdfs dfsadmin -refreshNodes`
5. 更新ResourceManager节点: `yarn rmadmin -refreshNodes`
6. 在web浏览器上查看旧节点是否退役
7. 如果数据不均衡，可以用命令实现集群的再平衡：`./start-balancer.sh`

### 黑名单退役
在黑名单上面的主机都会被强制退出。
1. 在NameNode的/opt/module/hadoop-3.1.3/etc/hadoop目录下创建dfs.hosts.exclude文件
	```bash
	vim dfs.hosts.exclude
	添加退役节点主机名
	hadoop105
	```
2. 在NameNode的hdfs-site.xml配置文件中增加dfs.hosts.exclude属性
	```xml
	<property>
		<name>dfs.hosts.exclude</name>
		  <value>/opt/module/hadoop-3.1.3/etc/hadoop/dfs.hosts.exclude</value>
	</property>

	```
	
3. 刷新NameNode、刷新ResourceManager
	```bash
	hdfs dfsadmin -refreshNodes
	yarn rmadmin -refreshNodes
	```
	
4. 检查Web浏览器，退役节点的状态为decommission in progress（退役中），说明数据节点正在复制块到其他节点
	![[700 Attachments/Pasted image 20220116170746.png]]
5. 等待退役节点状态为decommissioned（所有块已经复制完成），停止该节点及节点资源管理器。注意：如果副本数是3，服役的节点小于等于3，是不能退役成功的，需要修改副本数后才能退役
	![[700 Attachments/Pasted image 20220116170816.png]]
	```bash
	hdfs --daemon stop datanode
	sbin/yarn-daemon.sh stop nodemanager
	```
6. 如果数据不均衡，可以用命令实现集群的再平衡: `sbin/start-balancer.sh `

> 注意：不允许白名单和黑名单中同时出现同一个主机名称。