---
Create: 2022年 三月 9日, 星期三 09:55
tags: 
  - Engineering/spark
  - 大数据
---

# 简介
Standalone模式是Spark自带的资源调动引擎，构建一个由Master + Slave构成的Spark集群，Spark运行在集群中。

这个要和Hadoop中的Standalone区别开来。这里的Standalone是指只用Spark来搭建一个集群，不需要借助其他的框架。是相对于Yarn和Mesos来说的。

# 安装使用
1. 
|       | hadoop102    | hadoop103 | hadoop104 |
| ----- | ------------ | --------- | --------- |
| Spark | MasterWorker | Worker    | Worker    |

2. 解压一份Spark安装包，并修改解压后的文件夹名称为spark-standalone
	```bash
	tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz -C /opt/module/
	mv spark-2.1.1-bin-hadoop2.7 spark-standalone
	```
3. 进入Spark的配置目录/opt/module/spark-standalone/conf
4. 修改slave文件，添加work节点：
	```bash
	mv slaves.template slaves
	vim slaves
	hadoop102
	hadoop103
	hadoop104
	```
5. 修改spark-env.sh文件，添加master节点
	```bash
	mv spark-env.sh.template spark-env.sh
	vim spark-env.sh
	SPARK_MASTER_HOST=hadoop102
	SPARK_MASTER_PORT=7077
	```
6. 分发spark-standalone包 `xsync spark-standalone/`
7. 启动spark集群: `sbin/start-all.sh`
8. 查看三台服务器运行进程
	> 如果遇到 “JAVA_HOME not set” 异常，可以在sbin目录下的spark-config.sh 文件中加入如下配置：export JAVA_HOME=XXXX

9. 网页查看：hadoop102:8080（master web的端口，相当于hadoop的9870端口）
	目前还看不到任何任务的执行信息。
	
10. 官方求PI案例
	```bash
	bin/spark-submit \
	--class org.apache.spark.examples.SparkPi \
	--master spark://hadoop102:7077 \
	./examples/jars/spark-examples_2.11-2.1.1.jar \
	10
	```
	> 参数：--master spark://hadoop102:7077指定要连接的集群的master

10. 页面查看http://hadoop102:8080/，发现执行本次任务，默认采用三台服务器节点的总核数24核，每个节点内存1024M。
	> 8080：master的webUI
	> 4040：application的webUI的端口号

# 任务参数说明

| 参数                     | 解释                                                         | 可选值举例                                       |
| ------------------------ | ------------------------------------------------------------ | ------------------------------------------------ |
| --class                  | Spark程序中包含主函数的类                                    |                                                  |
| --master                 | Spark程序运行的模式                                          | 本地模式：local\[\*\]、spark://hadoop102:7077、Yarn |
| --executor-memory 1G     | 指定每个executor可用内存为1G                                 | 符合集群内存配置即可，具体情况具体分析。         |
| --total-executor-cores 2 | 指定**所有**executor使用的cpu核数为2个                       |                                                  |
| application-jar          | 打包好的应用jar，包含依赖。这个URL在集群中全局可见。 比如hdfs:// 共享存储系统，如果是file:// path，那么所有的节点的path都包含同样的jar |                                                  |
| application-arguments    | 传给main()方法的参数                                         |                                                  |



# 配置历史服务
由于spark-shell停止掉后，hadoop102:4040页面就看不到历史任务的运行情况，所以开发时都配置历史服务器记录任务运行情况。

1. 修改spark-default.conf.template名称
	```bash
	mv spark-defaults.conf.template spark-defaults.conf
	```
2. 修改spark-default.conf文件，配置日志存储路径，并分发`xsync spark-defaults.conf`
	```bash
	vi spark-defaults.conf
	spark.eventLog.enabled          true
	spark.eventLog.dir               hdfs://hadoop102:8020/directory
	```
> 需要启动Hadoop集群，HDFS上的目录需要提前存在。
> sbin/start-dfs.sh
> hadoop fs -mkdir /directory
3. 修改spark-env.sh文件，添加如下配置：
	```bash
	vi spark-env.sh

	export SPARK_HISTORY_OPTS="
	-Dspark.history.ui.port=18080 
	-Dspark.history.fs.logDirectory=hdfs://hadoop102:8020/directory 
	-Dspark.history.retainedApplications=30"
	```
	> 参数1含义：WEBUI访问的端口号为18080
	> 参数2含义：指定历史服务器日志存储路径
	> 参数3含义：指定保存Application历史记录的个数，如果超过这个值，旧的应用程序信息将被删除，这个是内存中的应用数，而不是页面上显示的应用数。

4. 分发配置文件：`xsync spark-env.sh`
5. 启动历史服务: `sbin/start-history-server.sh`
6. 再次执行任务:
	```bash
	bin/spark-submit \
	--class org.apache.spark.examples.SparkPi \
	--master spark://hadoop102:7077 \
	--executor-memory 1G \
	--total-executor-cores 2 \
	./examples/jars/spark-examples_2.11-2.1.1.jar \
	10
	```
	
7. 查看Spark历史服务地址：hadoop102:18080


# 配置高可用
## 高可用原理
![[700 Attachments/Pasted image 20220309102057.png]]

## 配置高可用
1. 停止集群：sbin/stop-all.sh
2. Zookeeper正常安装并启动（基于以前讲的数仓项目脚本）：zk.sh start
3. 修改spark-env.sh文件添加如下配置：
	```bash
	vi spark-env.sh

	注释掉如下内容：
	#SPARK_MASTER_HOST=hadoop102
	#SPARK_MASTER_PORT=7077

	添加上如下内容。配置由Zookeeper管理Master，在Zookeeper节点中自动创建/spark目录，用于管理：
	export SPARK_DAEMON_JAVA_OPTS="
	-Dspark.deploy.recoveryMode=ZOOKEEPER 
	-Dspark.deploy.zookeeper.url=hadoop102,hadoop103,hadoop104 
	-Dspark.deploy.zookeeper.dir=/spark"

	添加如下代码
	Zookeeper3.5的AdminServer默认端口是8080，和Spark的WebUI冲突
	export SPARK_MASTER_WEBUI_PORT=8989
	```

4. 分发配置文件:`xsync spark-env.sh`
5. 在hadoop102上启动全部节点: `sbin/start-all.sh`
6. 在hadoop103上单独启动master节点:`sbin/start-master.sh`
7. 在启动一个hadoop102窗口，将/opt/module/spark-local/input数据上传到hadoop集群的/input目录 :`hadoop fs -put /opt/module/spark-local/input/ /input`

8. Spark HA集群访问
	```bash
	bin/spark-shell \
	--master spark://hadoop102:7077,hadoop103:7077 \
	--executor-memory 2g \
	--total-executor-cores 2
	```
	> 参数：--master spark://hadoop102:7077指定要连接的集群的master

9. 执行WordCount程序
	```bash
	scala>sc.textFile("hdfs://hadoop102:8020/input").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect

	res0: Array[(String, Int)] = Array((hadoop,6), (oozie,3), (spark,3), (hive,3), (atguigu,3), (hbase,6))

	```
## 高可用测试

1. 查看hadoop102的master进程：jps
	```bash
	5506 Worker
	5394 Master
	5731 SparkSubmit
	4869 QuorumPeerMain
	5991 Jps
	5831 CoarseGrainedExecutorBackend
	```
2. Kill掉hadoop102的master进程`kill -9 5394`，页面中观察http://hadoop103:8080/的状态是否切换为active。
3. 再启动hadoop102的master进程:`sbin/start-master.sh`


## 运行流程
Spark有standalone-client和standalone-cluster两种模式，主要区别在于：Driver程序的运行节点。

### 客户端模式
```bash
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master spark://hadoop102:7077,hadoop103:7077 \
--executor-memory 2G \
--total-executor-cores 2 \
--deploy-mode client \
./examples/jars/spark-examples_2.11-2.1.1.jar \
10
```

--deploy-mode ==client==，表示Driver程序运行在本地客户端

![[700 Attachments/Pasted image 20220309132656.png]]

### 集群模式
```bash
bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master spark://hadoop102:7077,hadoop103:7077 \
--executor-memory 2G \
--total-executor-cores 2 \
--deploy-mode cluster \
./examples/jars/spark-examples_2.11-2.1.1.jar \
10
```
--deploy-mode ==cluster==，表示Driver程序运行在集群

![[700 Attachments/Pasted image 20220309132827.png]]

