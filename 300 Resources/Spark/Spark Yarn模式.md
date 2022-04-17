---
Create: 2022年 三月 9日, 星期三 13:29
tags: 
  - Engineering/spark
  - 大数据
---

# 简介
Spark客户端直接连接Yarn，不需要额外构建Spark集群。

# 安装使用
1. 停止Standalone模式下的spark集群
	```bash
	sbin/stop-all.sh
	zk.sh stop
	sbin/stop-master.sh
	```
2. 解压spark:` tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz -C /opt/module/`

3. 进入到/opt/module目录，修改spark-2.1.1-bin-hadoop2.7名称为spark-yarn:`mv spark-2.1.1-bin-hadoop2.7/ spark-yarn`
4. 修改hadoop配置文件/opt/module/hadoop-2.7.2/etc/hadoop/yarn-site.xml，添加如下内容

	```bash
	vi yarn-site.xml
	<!--是否启动一个线程检查每个任务正使用的物理内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
	<property>
		 <name>yarn.nodemanager.pmem-check-enabled</name>
		 <value>false</value>
	</property>

	<!--是否启动一个线程检查每个任务正使用的虚拟内存量，如果任务超出分配值，则直接将其杀掉，默认是true -->
	<property>
		 <name>yarn.nodemanager.vmem-check-enabled</name>
		 <value>false</value>
	</property>

	```
5. 分发配置文件:`xsync /opt/module/hadoop-2.7.2/etc/hadoop/yarn-site.xml`

6. 修改/opt/module/spark/conf/spark-env.sh，添加YARN_CONF_DIR配置，保证后续运行任务的路径都变成集群路径

	```bash
	mv spark-env.sh.template spark-env.sh
	vi spark-env.sh
	YARN_CONF_DIR=/opt/module/hadoop-2.7.2/etc/hadoop
	```

7. 分发spark-yarn:`xsync spark-yarn`
8. 启动HDFS以及YARN集群
	```bash
	sbin/start-dfs.sh
	sbin/start-yarn.sh
	```

9. 执行一个程序
	```bash
	bin/spark-submit \
	--class org.apache.spark.examples.SparkPi \
	--master yarn \
	./examples/jars/spark-examples_2.11-2.1.1.jar \
	10
	```
	> 参数：--master yarn，表示Yarn方式运行；--deploy-mod表示客户端方式运行程序

如果运行的时候，抛出如下异常:`ClassNotFoundException:com.sun.jersey.api.client.config.ClientConfig`
==原因分析==:Spark2中jersey版本是2.22，但是yarn中还需要依赖1.9，版本不兼容
==解决方式==:在yarn-site.xml中，添加
```bash
<property>
	<name>yarn.timeline-service.enabled</name>
	<value>false</value>
</property>	
```

## 配置历史服务
1. 修改spark-default.conf.template名称`mv spark-defaults.conf.template spark-defaults.conf`
2. 修改spark-default.conf文件，配置日志存储路径，并分发`xsync spark-defaults.conf`
	```bash
	vi spark-defaults.conf
	spark.eventLog.enabled          true
	spark.eventLog.dir               hdfs://hadoop102:8020/directory
	```
3. 修改spark-env.sh文件，添加如下配置：

	```bash
	vi spark-env.sh

	export SPARK_HISTORY_OPTS="
	-Dspark.history.ui.port=18080 
	-Dspark.history.fs.logDirectory=hdfs://hadoop102:8020/directory 
	-Dspark.history.retainedApplications=30"
	```
		>参数1含义：WEBUI访问的端口号为18080
		>参数2含义：指定历史服务器日志存储路径
		>参数3含义：指定保存Application历史记录的个数，如果超过这个值，旧的应用程序信息将被删除，这个是内存中的应用数，而不是页面上显示的应用数。

4. 分发配置文件:`xsync spark-env.sh`

## 配置查看历史日志

为了从Yarn上关联到Spark历史服务器，需要配置关联路径。
1. 修改配置文件/opt/module/spark/conf/spark-defaults.conf,添加如下内容：
	```bash
	spark.yarn.historyServer.address=hadoop102:18080
	spark.history.ui.port=18080
	```
	
2. 同步spark-defaults.conf配置文件:`xsync spark-defaults.conf`
3. 重启Spark历史服务:
	```bash
	sbin/stop-history-server.sh 
	sbin/start-history-server.sh 
	```
	
4. 提交任务到Yarn执行:
	```bash
	bin/spark-submit \
	--class org.apache.spark.examples.SparkPi \
	--master yarn \
	./examples/jars/spark-examples_2.11-2.1.1.jar \
	10
	```
	
5. Web页面查看日志：http://hadoop103:8088/cluster


## 运行流程
Spark有yarn-client和yarn-cluster两种模式，主要区别在于：Driver程序的运行节点。
yarn-client：Driver程序运行在客户端，适用于交互、调试，希望立即看到app的输出。
yarn-cluster：Driver程序运行在由ResourceManager启动的APPMaster适用于生产环境。

### 客户端模式（默认）
```bash
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--deploy-mode client \
./examples/jars/spark-examples_2.11-2.1.1.jar \
10
```

![[700 Attachments/Pasted image 20220310101752.png]]

### 集群模式
```bash
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--deploy-mode cluster \
./examples/jars/spark-examples_2.11-2.1.1.jar \
10
```
1. 查看http://hadoop103:8088/cluster页面，点击History按钮，跳转到历史详情页面 ![[700 Attachments/Pasted image 20220310101937.png]]
2. 点击Executors->点击driver中的stdout:![[700 Attachments/Pasted image 20220310101959.png]]![[700 Attachments/Pasted image 20220310102007.png]]可能碰到的问题:如果在 yarn 日志端无法查看到具体的日志, 则在yarn-site.xml中添加如下配置并启动Yarn历史服务器![[700 Attachments/Pasted image 20220310102048.png]]
	```bash
	<property>
		<name>yarn.log.server.url</name>
		<value>http://hadoop204:19888/jobhistory/logs</value>
	</property>
	```

> 注意：hadoop历史服务器也要启动 mr-jobhistory-daemon.sh start historyserver

![[700 Attachments/Pasted image 20220310102353.png]]