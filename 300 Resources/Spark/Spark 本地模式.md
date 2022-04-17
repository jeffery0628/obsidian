---
Create: 2021年 十二月 1日, 星期三 13:15
tags: 
  - Engineering/spark
  - 大数据
---

[官网下载](https://archive.apache.org/dist/spark/)

# 预配置
1. [[300 Resources/java/搭建java 环境|搭建java 环境]]
2. [[300 Resources/scala/搭建本地scala环境|搭建scala环境]]







# 安装 spark Local 模式

Local 模式就是指的只在一台计算机上来运行 Spark。通常用于测试的目的来使用 Local 模式, 实际的生产环境中不会使用 Local 模式。

## 安装
1. 到官网下载安装包：[下载地址](https://spark.apache.org/downloads.html)
2. 解压下载的安装包,并命名为spark-local：
	```bash
	tar -zxvf spark-2.1.1-bin-hadoop2.7.tgz -C /opt/module  
	cp -r spark-2.1.1-bin-hadoop2.7 spark-local
	```
3. 设置路径和环境变量: `vim ~/.bash_profile`
	```bash
	export SPARK_HOME=SPARK_HOME=/Users/lizhen/Documents/dev_env/spark-local
	export PATH=${SPARK_HOME}/bin:$PATH
	```
4. 让配置生效：`source ~/.bash_profile`
5. 验证安装是否成功：
	```bash
	cd spark-local
	./bin/spark-shell
	```

	测试运行：
	```bash
	bin/spark-submit --class org.apache.spark.examples.SparkPi --master local[2] ./examples/jars/spark-examples_2.11-2.1.1.jar 100
	```

## 运行参数介绍：
> --master： 指定 **master** 的地址，默认为**local**. 表示在本机运行.
> 
> --class：应用的启动类 (如 **org.apache.spark.examples.SparkPi**)
> 
> --deploy-mode：是否发布驱动到 **worker**节点(**cluster** 模式) 或者作为一个本地客户端 (**client** 模式) (**default: client**)
> 
> --conf: 任意的 Spark 配置属性， 格式**key=value**. 如果值包含空格，可以加引号**"key=value"**
> 
> application-jar: 打包好的应用 jar,包含依赖. 这个 URL 在集群中全局可见。
> 
> application-arguments: 传给**main()**方法的参数
> 
> --executor-memory 1G 指定每个**executor**可用内存为1G
> 
> --total-executor-cores 6 指定所有**executor**使用的cpu核数为6个
> 
> --executor-cores： 表示每个**executor**使用的 cpu 的核数

Master URL

**local**:在本地使用一个worker运行spark ，不使用并行计算。

**local[K]**:在本地使用k个worker 运行spark，k个线程一起计算。

**local\[\*\]**:在本地使用尽可能多的线程作为逻辑core 运行spark。

**spark://HOST:PORT**:连接给定的spark standalone cluster master ，需要配置端口，默认7077。

**mesos://HOST:PORT**:连接给定的Mesos 集群管理，需要配置端口，默认5050。

**yarn**:连接YARN集群管理，需配置HADOOP_CONF_DIR YARN_CONF_DIR。

## spark-shell

 bin/spark-shell

## 通过web ui查看程序运行情况
 http://localhost:4040/jobs/