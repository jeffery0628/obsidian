---
Create: 2022年 四月 16日, 星期六 16:37
tags: 
  - Engineering/kafka
  - 大数据
---
1. 修改kafka启动命令
	修改kafka-server-start.sh命令中：
	```bash
	if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
		export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
	fi
	```
	为
	```
	if [ "x$KAFKA_HEAP_OPTS" = "x" ]; then
		export KAFKA_HEAP_OPTS="-server -Xms2G -Xmx2G -XX:PermSize=128m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=8 -XX:ConcGCThreads=5 -XX:InitiatingHeapOccupancyPercent=70"
		export JMX_PORT="9999"
		#export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"
	fi

	```
	> 注意：修改之后在启动Kafka之前要分发之其他节点

2. 上传压缩包kafka-eagle-bin-1.3.7.tar.gz到集群/opt/software目录

3. 解压到本地` tar -zxvf kafka-eagle-bin-1.3.7.tar.gz`
4. 进入刚才解压的目录:
5. 将kafka-eagle-web-1.3.7-bin.tar.gz解压至/opt/module
6. 修改名称`mv kafka-eagle-web-1.3.7/ eagle`
7. 给启动文件执行权限: `cd bin/  chmod 777 ke.sh`
8. 修改配置文件: 
	```
	######################################
	# multi zookeeper&kafka cluster list
	######################################
	kafka.eagle.zk.cluster.alias=cluster1
	cluster1.zk.list=hadoop102:2181,hadoop103:2181,hadoop104:2181

	######################################
	# kafka offset storage
	######################################
	cluster1.kafka.eagle.offset.storage=kafka

	######################################
	# enable kafka metrics
	######################################
	kafka.eagle.metrics.charts=true
	kafka.eagle.sql.fix.error=false

	######################################
	# kafka jdbc driver address
	######################################
	kafka.eagle.driver=com.mysql.jdbc.Driver
	kafka.eagle.url=jdbc:mysql://hadoop102:3306/ke?useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull
	kafka.eagle.username=root
	kafka.eagle.password=000000
	```

9. 添加环境变量
	```
	export KE_HOME=/opt/module/eagle
	export PATH=$PATH:$KE_HOME/bin
	```
	> 注意：source /etc/profile

10. 启动 `bin/ke.sh start`
11. 登录页面查看监控数据: http://192.168.9.102:8048/ke

