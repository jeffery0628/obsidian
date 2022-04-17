---
Create: 2022年 三月 8日, 星期二 22:44
tags: 
  - Engineering/scala
  - 大数据
---

 

# 安装步骤
1. 首先确保JDK1.8安装成功
2. 下载对应的Scala安装文件scala-2.11.8.zip
3. 解压scala-2.11.8.zip，我这里解压到E:\02_software
4. 配置Scala的环境变量

# 预配置
[[300 Resources/java/搭建java 环境|搭建java 环境]]


# 配置scala环境
1. 到官网下载安装包：[下载地址](https://www.scala-lang.org/download/)
2. 解压缩到指定目录:  `sudo tar -zxvf scala-2.11.8.tgz -C /usr/lib/scala`
3. 设置路径和环境变量: `vim ~/.bash_profile`
	```bash
	export SCALA_HOME=SCALA_HOME=/Users/lizhen/Documents/dev_env/scala-2.11.8  //版本号视自己安装的而定
	export PATH=${SCALA_HOME}/bin:$PATH
	```
4. 让配置生效： `source ~/.bash_profile`
5. 验证安装是否成功：`scala`