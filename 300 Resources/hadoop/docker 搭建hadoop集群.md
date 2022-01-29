---
Create: 2022年 一月 3日, 星期一 14:12
tags: 
  - Engineering/hadoop
  - 大数据
---
## 创建工程目录
```bash
mkdir hadoop_docker
```

## 准备安装包
1. apache-zookeeper-3.7.0-bin.tar.gz 
2. hadoop-3.1.3.tar.gz 
3. jdk-8u301-linux-x64.tar.gz

## 创建dockerfile

```dockerfile
FROM centos:centos7.9.2009
LABEL maintainer='1198727509'
ADD jdk-8u301-linux-x64.tar.gz  /root 
ADD apache-zookeeper-3.7.0-bin.tar.gz  /root 
ADD hadoop-3.1.3.tar.gz /root 

RUN yum install -y epel-release \
	&& yum install -y openssh-server sudo \
	&& sed -i 's/UsePAM yes/UsePAM no/g' /etc/ssh/sshd_config \
	&& yum install -y openssh-clients psmisc nc net-tools rsync vim lrzsz ntp libzstd openssl-static tree iotop git\
	&& echo "root:root" | chpasswd \
	&& echo "root   ALL=(ALL)       ALL" >> /etc/sudoers \
	&& ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key \
	&& ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key \
	&& mkdir /var/run/sshd \
	&& mv /root/jdk1.8.0_301 /root/jdk8 \
	&& mv /root/apache-zookeeper-3.7.0-bin /root/zookeeper \
	&& mv /root/hadoop-3.1.3 /root/hadoop 
EXPOSE 22
CMD ["/usr/sbin/sshd", "-D"]

ENV JAVA_HOME=/root/jdk8 ZOOKEEPER_HOME=/root/zookeeper HADOOP_HOME=/root/hadoop 
ENV PATH=$JAVA_HOME/bin:$ZOOKEEPER_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH
```

## 执行dockerfile 创建镜像
```bash
 docker build . -t hadoop
 ```

## 创建虚拟网络
创建：
```bash
docker network create --subnet=192.168.1.0/24 hadoopnet
```

查看：
```bash
docker network ls
```

## 启动容器
```bash
docker run --name hadoop01 --hostname hadoop01 -d -P -p 50070:50070 -p 8088:8088 -p 20000:22 --network  hadoopnet  --network-alias hadoop01 --ip 192.168.1.101 -v /home/lizhen/data:/data hadoop:latest

```

```bash
docker run --name hadoop02 --hostname hadoop02 -d -P -p 20001:22 --network  hadoopnet --network-alias hadoop02 --ip 192.168.1.102 -v /home/lizhen/data:/data hadoop:latest
```

```bash
docker run --name hadoop03 --hostname hadoop03 -d -P -p 20002:22 --network  hadoopnet --network-alias hadoop03 --ip 192.168.1.103 -v /home/lizhen/data:/data hadoop:latest
```

```bash
docker run --name hadoop04 --hostname hadoop04 -d -P -p 20003:22 --network  hadoopnet --network-alias hadoop04 --ip 192.168.1.104 -v /home/lizhen/data:/data hadoop:latest
```

```bash
docker run --name hadoop05 --hostname hadoop05 -d -P -p 20004:22 --network  hadoopnet --network-alias hadoop05 --ip 192.168.1.105 -v /home/lizhen/data:/data hadoop:latest

```
## 查看网络
```bash
docker network inspect hadoopnet
```

## 查看容器
```bash
docker ps -a
```

## 进入容器
```bash
docker exec -it hadoop01 /bin/bash
```
```bash
docker exec -it hadoop02 /bin/bash
```
```bash
docker exec -it hadoop03 /bin/bash
```
```bash
docker exec -it hadoop04 /bin/bash
```
```bash
docker exec -it hadoop05 /bin/bash
```

```bash
vim /etc/hosts
```

```vim
192.168.1.101   hadoop01
192.168.1.102   hadoop02
192.168.1.103   hadoop03
192.168.1.104   hadoop04
192.168.1.105   hadoop05
192.168.1.106   hadoop06
192.168.1.107   hadoop07
192.168.1.108   hadoop08
```
## ssh免密登录
```bash
ssh-keygen #一路回车
```


输入yes,密码为Dockerfile建立时的密码:root
```bash
ssh-copy-id hadoop01
ssh-copy-id hadoop02
ssh-copy-id hadoop03
ssh-copy-id hadoop04
ssh-copy-id hadoop05
```





## 集群分发脚本 xsync
### scp
定义：scp可以实现服务器与服务器之间的数据拷贝。
基本语法：
```bash
scp    -r          $pdir/$fname              $user@hadoop$host:$pdir/$fname
命令   递归       要拷贝的文件路径/名称    目的用户@主机:目的路径/名称
```

### rsync 远程同步工具
rsync主要用于备份和镜像。具有速度快、避免复制相同内容和支持符号链接的优点。
rsync和scp区别：用rsync做文件的复制要比scp的速度快，rsync只对差异文件做更新。scp是把所有文件都复制过去。

基本语法：
```bash
rsync    -av       $pdir/$fname              $user@hadoop$host:$pdir/$fname
命令   选项参数   要拷贝的文件路径/名称    目的用户@主机:目的路径/名称
```
1. -a :归档拷贝
2. -v：显示复制过程
### xsync集群分发脚本
需求：循环复制文件到所有节点的相同目录下
需求分析：rsync命令原始拷贝。
> 说明：在/bin这个目录下存放的脚本，用户可以在系统任何地方直接执行。

#### 脚本实现
1. 在/home/lizhen目录下创建xsync文件
	```vim
	cd /bin
	vim xsync
	```
	在文件中编写如下代码：
	```bash
	#!/bin/bash
	#1. 判断参数个数
	if [ $# -lt 1 ]
	then
	  echo Not Enough Arguement!
	  exit;
	fi
	#2. 遍历集群所有机器
	for host in hadoop01 hadoop02 hadoop03 hadoop04 hadoop05
	do
	  echo ====================  $host  ====================
	  #3. 遍历所有目录，挨个发送
	  for file in $@
	  do
		#4 判断文件是否存在
		if [ -e $file ]
		then
		  #5. 获取父目录
		  pdir=$(cd -P $(dirname $file); pwd)
		  #6. 获取当前文件的名称
		  fname=$(basename $file)
		  ssh $host "mkdir -p $pdir"
		  rsync -av $pdir/$fname $host:$pdir
		else
		  echo $file does not exists!
		fi
	  done
	done

	
	```

2. 修改脚本xsync具有执行权限
	```bash
	chmod +x xsync
	```
	
3. 测试脚本
	```bash
	sudo xsync /bin/xsync
	```


## Zookeeper配置
5个容器均进行此操作
1. 创建数据和日志文件夹
	```bash
	cd /root/zookeeper/
	mkdir data
	mkdir logs
	```

	```bash
	cd data
	```

2. 新建myid文件并写入id号
	1. hadoop01：id号’1’ : echo '1'>myid
	2. hadoop02：id号’2’: echo '2'>myid
	3. hadoop03：id号’3’:echo '3'>myid
	4. hadoop04：id号’4’:echo '4'>myid
	5. hadoop05：id号’5’: echo '5'>myid

3. 进入conf文件夹并将zoo_sample.cfg复制为zoo.cfg
	```bash
	cd /root/zookeeper/conf/
	cp zoo_sample.cfg zoo.cfg
	vim zoo.cfg
	```
	修改项如下：
	```cfg
	dataDir=/root/zookeeper/data
	```
	在文件末尾新增：
	```cfg
	dataLogDir=/root/zookeeper/logs
	server.1=hadoop01:2888:3888
	server.2=hadoop02:2888:3888
	server.3=hadoop03:2888:3888
	server.4=hadoop04:2888:3888
	server.5=hadoop05:2888:3888
	```

4. 同步 ：
	```bash
	sudo xsync /root/zookeeper/
	```

6. 修改每个机器上对应的myid

7. 启动zookeeper(5个节点均要执行)：
	```bash
	
	zkServer.sh start   # 看到下列输出，则启动成功：
	```
	![[700 Attachments/Pasted image 20220103172816.png]]
