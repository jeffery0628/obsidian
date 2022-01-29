---
Create: 2022年 一月 20日, 星期四 10:16
tags: 
  - Engineering/hadoop
  - 大数据
---


## 配置Zookeeper集群
1. 集群规划：在hadoop101、hadoop102、hadoop103三个节点上部署Zookeeper
2. 解压安装
	1. 解压zookeeper安装包到/opt/module目录下
		```bash
		tar -zxvf zookeeper-3.4.1.tar.gz -C /opt/module
		```
	2. 在/opt/module/zookeeper-3.4.14/这个目录下创建zkData
		```bash
		mkdir -p zkData
		```
	3. 重命名/opt/module/zookeeper-3.4.14/conf这个目录下的zoo_sample.cfg为zoo.cfg
		```bash
		mv zoo_sample.cfg zoo.cfg
		```
3. 配置zoo.cfg文件
	1. 具体配置dataDir=/opt/module/zookeeper-3.4.14/zkData
		```
		#######################cluster##############
		server.2=hadoop101:2888:3888
		server.3=hadoop102:2888:3888
		server.4=hadoop103:2888:3888
		```
		> Server.A=B:C:D。
		> A是一个数字，表示这个是第几号服务器；
		> B是这个服务器的IP地址；
		> C是这个服务器与集群中的Leader服务器交换信息的端口；
		> D是万一集群中的Leader服务器挂了，需要一个端口来重新进行选举，选出一个新的Leader，而这个端口就是用来执行选举时服务器相互通信的端口。
		> 集群模式下配置一个文件myid，这个文件在dataDir目录下，这个文件里面有一个数据就是A的值，Zookeeper启动时读取此文件，拿到里面的数据与zoo.cfg里面的配置信息比较从而判断到底是哪个server。

4. 集群操作
	1. 在/opt/module/zookeeper-3.4.14/zkData目录下创建一个myid的文件
	```
	touch myid
	```
	2. 编辑myid文件，在文件中添加与server对应的编号：如2
	3. 拷贝配置好的zookeeper到其他机器上
		```bash
		scp -r zookeeper-3.4.14/ root@hadoop102.lizhen.com:/opt/app/
		scp -r zookeeper-3.4.14/ root@hadoop103.lizhen.com:/opt/app/

		```
	4. 分别启动zookeeper: /bin/zkServer.sh start
	5. 查看状态: bin/zkServer.sh status



