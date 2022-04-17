---
Create: 2022年 三月 8日, 星期二 22:36
tags: 
  - Engineering/java
  - 大数据
---

1. 官网下载JDK :[下载地址](https://www.oracle.com/java/technologies/downloads/#java8)

2. 解压缩到指定的目录: `sudo tar -zxvf jdk-8u91-linux-x64.tar.gz -C /usr/lib/jdk`
3. 设置路径和环境变量 `vim ~/.bash_profile`

	```bash
	export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_301.jdk/Contents/Home
	export CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.
	export PATH=$JAVA_HOME/bin:$PATH
	```

4. 让配置生效 `srouce ~/.bash_profile`
5. 验证安装是否成功: `java -version`