---
Create: 2022年 一月 20日, 星期四 10:19
tags: 
  - Engineering/zookeeper
  - 大数据
---

## 本地模式安装部署

### 环境准备
1. 安装Jdk
2. 拷贝Zookeeper安装包到Linux系统下
3. 解压到指定目录
	```bash
	tar -zxvf zookeeper-3.5.7.tar.gz -C /opt/module/
	```
	
### 配置修改
1. 将/opt/module/zookeeper-3.5.7/conf这个路径下的zoo_sample.cfg修改为zoo.cfg；
2. 打开zoo.cfg文件，修改dataDir路径：
	```bash
	vim zoo.cfg
	dataDir=/opt/module/zookeeper-3.5.7/zkData
	```
3. 在/opt/module/zookeeper-3.5.7/这个目录上创建zkData文件夹

### 操作Zookeeper
1. 启动Zookeeper
	```bash
	bin/zkServer.sh start
	```
2. 查看进程是否启动:
	```bash
	jps

	4020 Jps
	4001 QuorumPeerMain
	```
3. 查看状态：
	 ```bash
	 bin/zkServer.sh status
	```
4. 启动客户端
	```bash
	bin/zkCli.sh
	```
5. 退出客户端：quit
6. 停止Zookeeper :  bin/zkServer.sh stop

## 配置参数解读
Zookeeper中的配置文件zoo.cfg中参数含义解读如下：
1. tickTime =2000：通信心跳数，Zookeeper服务器与客户端心跳时间，单位毫秒。Zookeeper使用的基本时间，服务器之间或客户端与服务器之间维持心跳的时间间隔，也就是每个tickTime时间就会发送一个心跳，时间单位为毫秒。它用于心跳机制，并且设置最小的session超时时间为两倍心跳时间。(session的最小超时时间是2*tickTime)
2. initLimit =10：LF初始通信时限
	集群中的Follower跟随者服务器与Leader领导者服务器之间初始连接时能容忍的最多心跳数（tickTime的数量），用它来限定集群中的Zookeeper服务器连接到Leader的时限。
3. syncLimit =5：LF同步通信时限
	集群中Leader与Follower之间的最大响应时间单位，假如响应超过syncLimit * tickTime，Leader认为Follwer死掉，从服务器列表中删除Follwer。
4. dataDir：数据文件目录+数据持久化路径，主要用于保存Zookeeper中的数据。
5. clientPort =2181：客户端连接端口，监听客户端连接的端口。







