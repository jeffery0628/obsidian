---
Create: 2021年 十二月 26日, 星期日 17:27
tags: 
  - Engineering/hadoop
  - 大数据
---

## 拉取docker镜像
```bash
docker pull centos:centos7

```

## 自定义网络类型
```bash
docker network create --subnet=192.168.1.0/24 hadoopnet
```

> [[300 Resources/docker/docker 容器设置固定ip|docker 容器设置固定ip]]

## 启动容器(固定ip 固定主机名)
```bash
docker run -dit --name hadoop101 --hostname hadoop101 --net hadoopnet --ip 192.168.1.101 -v /home/lizhen/data:/data centos:centos7 /bin/bash

```

## 容器安装必要软件
```bash
yum install -y epel-release
yum install -y psmisc nc net-tools rsync vim lrzsz ntp libzstd openssl-static tree iotop git wget

```
## 配置主机映射
```bash
vim /etc/hosts

192.168.1.100   hadoop100
192.168.1.101   hadoop101
192.168.1.102   hadoop102
192.168.1.103   hadoop103
192.168.1.104   hadoop104
192.168.1.105   hadoop105
192.168.1.106   hadoop106
192.168.1.107   hadoop107
192.168.1.108   hadoop108
192.168.1.109   hadoop109
```

## 关闭防火墙
```bash
systemctl stop firewalld
systemctl disable firewalld
```

## 创建用户
```bash
sudo useradd lizhen
sudo passwd lizhen
```

### 配置用户sudo权限
```bash
vim /etc/sudoers

## Allow root to run any commands anywhere
root    ALL=(ALL)     ALL
lizhen   ALL=(ALL)     ALL
```

## 在/opt目录下创建文件夹
```bash
mkdir /opt/module /opt/software

# sudo chown lizhen:lizhen /opt/module /opt/software
```


## 安装JDK
```bash
cd /data
tar -zxvf jdk-8u301-linux-x64.tar.gz  -C /opt/module/
```




## 安装Hadoop
[下载地址](https://archive.apache.org/dist/hadoop/common/hadoop-3.1.3/)
```bash
tar -zxvf hadoop-3.1.3.tar.gz -C /opt/module/
```

### 配置环境变量
```bash
vim /etc/profile.d/my_env.sh

export HADOOP_HOME=/opt/module/hadoop-2.7.2
export JAVA_HOME=/opt/module/jdk1.8.0_301
export PATH=$PATH:$JAVA_HOME/bin
```
验证安装成功
1. source /etc/profile
2. 先退出docker
3. 登陆docker
4. java -version
5. hadoop version




