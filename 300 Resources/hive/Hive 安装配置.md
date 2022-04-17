---
Create: 2022年 四月 12日, 星期二 13:19
tags: 
  - Engineering/hive
  - 大数据
---
# Hive安装地址
1. [Hive官网地址](http://hive.apache.org/)
2. [文档查看地址](https://cwiki.apache.org/confluence/display/Hive/GettingStarted)
3. [下载地址](http://archive.apache.org/dist/hive/)
4. [github地址](https://github.com/apache/hive)

# Mysql 安装
## 安装包准备
1. 卸载自带的Mysql-libs（如果之前安装过mysql，要全都卸载掉）
	```bash
	$ rpm -qa | grep -i -E mysql\|mariadb | xargs -n1 sudo rpm -e --nodeps
	```
2. 将安装包和JDBC驱动上传到/opt/software，共计6个
	```
	01_mysql-community-common-5.7.29-1.el7.x86_64.rpm
	02_mysql-community-libs-5.7.29-1.el7.x86_64.rpm
	03_mysql-community-libs-compat-5.7.29-1.el7.x86_64.rpm
	04_mysql-community-client-5.7.29-1.el7.x86_64.rpm
	05_mysql-community-server-5.7.29-1.el7.x86_64.rpm
	mysql-connector-java-5.1.48.jar
	```

## 安装MySql
1. 安装mysql依赖
	```bash
	$ sudo rpm -ivh 01_mysql-community-common-5.7.29-1.el7.x86_64.rpm
	$ sudo rpm -ivh 02_mysql-community-libs-5.7.29-1.el7.x86_64.rpm
	$ sudo rpm -ivh 03_mysql-community-libs-compat-5.7.29-1.el7.x86_64.rpm
	```

2. 安装mysql-client
	```
	$ sudo rpm -ivh 04_mysql-community-client-5.7.29-1.el7.x86_64.rpm
	```

3. 安装mysql-server
	```
	$ sudo rpm -ivh 05_mysql-community-server-5.7.29-1.el7.x86_64.rpm
	```
4. 启动mysql:`$ sudo systemctl start mysqld`
5. 查看mysql密码: `$ sudo cat /var/log/mysqld.log | grep password`

## 配置MySql
配置只要是root用户+密码，在任何主机上都能登录MySQL数据库。
1. 用刚刚查到的密码进入mysql（如果报错，给密码加单引号）`mysql -uroot -p’password’`
2. 设置复杂密码(由于mysql密码策略，此密码必须足够复杂):`mysql> set password=password("Qs23=zs32");`
3. 更改mysql密码策略
	```
	mysql> set global validate_password_length=4;
	mysql> set global validate_password_policy=0;
	```
4. 设置简单好记的密码 `mysql> set password=password("000000");`
5. 进入msyql库: `mysql> use mysql`
6. 查询user表: `mysql> select user, host from user;`
7. 修改user表，把Host表内容修改为%:`mysql> update user set host="%" where user="root";`
8. 刷新: `mysql> flush privileges;`
9. 退出: `mysql> quit;`

# Hive安装部署
1. 把apache-hive-3.1.2-bin.tar.gz上传到linux的/opt/software目录下
2. 解压apache-hive-3.1.2-bin.tar.gz到/opt/module/目录下面:`$ tar -zxvf /opt/software/apache-hive-3.1.2-bin.tar.gz -C /opt/module/`
3. 修改apache-hive-3.1.2-bin.tar.gz的名称为hive: `$ mv /opt/module/apache-hive-3.1.2-bin/ /opt/module/hive`
4. 修改/etc/profile.d/my_env.sh，添加环境变量: 
	```
	$ sudo vim /etc/profile.d/my_env.sh
	添加内容：
	#HIVE_HOME
	export HIVE_HOME=/opt/module/hive
	export PATH=$PATH:$HIVE_HOME/bin
	```
5. 重启Xshell对话框使环境变量生效
6. 解决日志Jar包冲突
	```
	$ mv $HIVE_HOME/lib/log4j-slf4j-impl-2.10.0.jar $HIVE_HOME/lib/log4j-slf4j-impl-2.10.0.bak
	```
	

# Hive元数据配置到MySql
## 拷贝驱动
将MySQL的JDBC驱动拷贝到Hive的lib目录下
```
$ cp /opt/software/mysql-connector-java-5.1.48.jar $HIVE_HOME/lib
```

## 配置Metastore到MySql
在$HIVE_HOME/conf目录下新建hive-site.xml文件
```
$ vim $HIVE_HOME/conf/hive-site.xml
```
添加如下内容:
```
<?xml version="1.0"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
<configuration>
    <property>
        <name>javax.jdo.option.ConnectionURL</name>
        <value>jdbc:mysql://hadoop102:3306/metastore?useSSL=false</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionDriverName</name>
        <value>com.mysql.jdbc.Driver</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionUserName</name>
        <value>root</value>
    </property>

    <property>
        <name>javax.jdo.option.ConnectionPassword</name>
        <value>000000</value>
    </property>

    <property>
        <name>hive.metastore.warehouse.dir</name>
        <value>/user/hive/warehouse</value>
    </property>

    <property>
        <name>hive.metastore.schema.verification</name>
        <value>false</value>
    </property>

    <property>
        <name>hive.metastore.uris</name>
        <value>thrift://hadoop102:9083</value>
    </property>

    <property>
    <name>hive.server2.thrift.port</name>
    <value>10000</value>
    </property>

    <property>
        <name>hive.server2.thrift.bind.host</name>
        <value>hadoop102</value>
    </property>

    <property>
        <name>hive.metastore.event.db.notification.api.auth</name>
        <value>false</value>
    </property>

</configuration>

```

# 安装Tez引擎
Tez是一个Hive的运行引擎，性能优于MR。为什么优于MR呢？看下图。
![[700 Attachments/Pasted image 20220412134325.png]]
用Hive直接编写MR程序，假设有四个有依赖关系的MR作业，上图中，绿色是Reduce Task，云状表示写屏蔽，需要将中间结果持久化写到HDFS。

Tez可以将多个有依赖的作业转换为一个作业，这样只需写一次HDFS，且中间节点较少，从而大大提升作业的计算性能。

1. 将tez安装包拷贝到集群，并解压tar包
	```
	$ mkdir /opt/module/tez
	$ tar -zxvf /opt/software/tez-0.10.1-SNAPSHOT-minimal.tar.gz -C /opt/module/tez
	```
2. 上传tez依赖到HDFS
	```
	$ hadoop fs -mkdir /tez
	$ hadoop fs -put /opt/software/tez-0.10.1-SNAPSHOT.tar.gz /tez
	```
3. 新建tez-site.xml
	```
	$ vim $HADOOP_HOME/etc/hadoop/tez-site.xml	
	```
	添加如下内容：
	```
	<?xml version="1.0" encoding="UTF-8"?>
	<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
	<configuration>
	<property>
		<name>tez.lib.uris</name>
		<value>${fs.defaultFS}/tez/tez-0.10.1-SNAPSHOT.tar.gz</value>
	</property>
	<property>
		 <name>tez.use.cluster.hadoop-libs</name>
		 <value>true</value>
	</property>
	<property>
		 <name>tez.am.resource.memory.mb</name>
		 <value>1024</value>
	</property>
	<property>
		 <name>tez.am.resource.cpu.vcores</name>
		 <value>1</value>
	</property>
	<property>
		 <name>tez.container.max.java.heap.fraction</name>
		 <value>0.4</value>
	</property>
	<property>
		 <name>tez.task.resource.memory.mb</name>
		 <value>1024</value>
	</property>
	<property>
		 <name>tez.task.resource.cpu.vcores</name>
		 <value>1</value>
	</property>
	</configuration>
	```
4. 修改Hadoop环境变量
	```
	$ vim $HADOOP_HOME/etc/hadoop/shellprofile.d/tez.sh
	```
	添加Tez的Jar包相关信息
	```
	hadoop_add_profile tez
	function _tez_hadoop_classpath
	{
		hadoop_add_classpath "$HADOOP_HOME/etc/hadoop" after
		hadoop_add_classpath "/opt/module/tez/*" after
		hadoop_add_classpath "/opt/module/tez/lib/*" after
	}
	```
5. 修改Hive的计算引擎`vim $HIVE_HOME/conf/hive-site.xml`添加
	```
	<property>
		<name>hive.execution.engine</name>
		<value>tez</value>
	</property>
	<property>
		<name>hive.tez.container.size</name>
		<value>1024</value>
	</property>
	```
	
6. 解决日志Jar包冲突
	```
	$ rm /opt/module/tez/lib/slf4j-log4j12-1.7.10.jar
	```
	
# 启动Hive
## 初始化元数据库
1. 登陆MySQL`$ mysql -uroot -p000000`
2. 新建Hive元数据库
	```
	mysql> create database metastore;
	mysql> quit;
	```
3. 初始化Hive元数据库`$ schematool -initSchema -dbType mysql -verbose`

## 启动metastore和hiveserver2
1. Hive 2.x以上版本，要先启动这两个服务，否则会报错：
	```
	FAILED: HiveException java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient
	```
	
2. 编写hive服务启动脚本`$ vim $HIVE_HOME/bin/hiveservices.sh`
	```
	#!/bin/bash
	HIVE_LOG_DIR=$HIVE_HOME/logs

	mkdir -p $HIVE_LOG_DIR

	#检查进程是否运行正常，参数1为进程名，参数2为进程端口
	function check_process()
	{
		pid=$(ps -ef 2>/dev/null | grep -v grep | grep -i $1 | awk '{print $2}')
		ppid=$(netstat -nltp 2>/dev/null | grep $2 | awk '{print $7}' | cut -d '/' -f 1)
		echo $pid
		[[ "$pid" =~ "$ppid" ]] && [ "$ppid" ] && return 0 || return 1
	}

	function hive_start()
	{
		metapid=$(check_process HiveMetastore 9083)
		cmd="nohup hive --service metastore >$HIVE_LOG_DIR/metastore.log 2>&1 &"
		cmd=$cmd" sleep4; hdfs dfsadmin -safemode wait >/dev/null 2>&1"
		[ -z "$metapid" ] && eval $cmd || echo "Metastroe服务已启动"
		server2pid=$(check_process HiveServer2 10000)
		cmd="nohup hive --service hiveserver2 >$HIVE_LOG_DIR/hiveServer2.log 2>&1 &"
		[ -z "$server2pid" ] && eval $cmd || echo "HiveServer2服务已启动"
	}

	function hive_stop()
	{
		metapid=$(check_process HiveMetastore 9083)
		[ "$metapid" ] && kill $metapid || echo "Metastore服务未启动"
		server2pid=$(check_process HiveServer2 10000)
		[ "$server2pid" ] && kill $server2pid || echo "HiveServer2服务未启动"
	}

	case $1 in
	"start")
		hive_start
		;;
	"stop")
		hive_stop
		;;
	"restart")
		hive_stop
		sleep 2
		hive_start
		;;
	"status")
		check_process HiveMetastore 9083 >/dev/null && echo "Metastore服务运行正常" || echo "Metastore服务运行异常"
		check_process HiveServer2 10000 >/dev/null && echo "HiveServer2服务运行正常" || echo "HiveServer2服务运行异常"
		;;
	*)
		echo Invalid Args!
		echo 'Usage: '$(basename $0)' start|stop|restart|status'
		;;
	esac
	```
3. 添加执行权限`$ chmod +x $HIVE_HOME/bin/hiveservices.sh`
4. 启动Hive后台服务： `$ hiveservices.sh start`

## HiveJDBC访问
启动beeline客户端: `$ beeline -u jdbc:hive2://hadoop102:10000 -n atguigu`

看到界面如下：
```
Connecting to jdbc:hive2://hadoop102:10000
Connected to: Apache Hive (version 3.1.2)
Driver: Hive JDBC (version 3.1.2)
Transaction isolation: TRANSACTION_REPEATABLE_READ
Beeline version 3.1.2 by Apache Hive
0: jdbc:hive2://hadoop102:10000>
```

# Hive常用交互命令
1. help
	```
	$ bin/hive -help

	usage: hive
	 -d,--define <key=value>          Variable subsitution to apply to hive
									  commands. e.g. -d A=B or --define A=B
		--database <databasename>     Specify the database to use
	 -e <quoted-query-string>         SQL from command line
	 -f <filename>                    SQL from files
	 -H,--help                        Print help information
		--hiveconf <property=value>   Use value for given property
		--hivevar <key=value>         Variable subsitution to apply to hive
									  commands. e.g. --hivevar A=B
	 -i <filename>                    Initialization SQL file
	 -S,--silent                      Silent mode in interactive shell
	 -v,--verbose                     Verbose mode (echo executed SQL to the console)

	```

2. “-e”不进入hive的交互窗口执行sql语句
	```
	$ bin/hive -e "select id from student;"

	````

3. “-f”执行脚本中sql语句
	1. 在/opt/module/datas目录下创建hivef.sql文件 `$ touch hivef.sql`
	2. 文件中写入正确的sql语句`select *from student;`
	3. 执行文件中的sql语句`$ bin/hive -f /opt/module/datas/hivef.sql`
	4. 执行文件中的sql语句并将结果写入文件中 `bin/hive -f /opt/module/datas/hivef.sql  > /opt/module/datas/hive_result.txt`


# Hive其他命令操作
1. 退出hive窗口：
	```
	hive(default)>exit;
	hive(default)>quit;
	```
2. 在hive cli命令窗口中如何查看hdfs文件系统:`hive(default)>dfs -ls /;`
3. 查看在hive中输入的所有历史命令:进入到当前用户的home/user目录，查看. hivehistory文件`cat .hivehistory`

# Hive常见属性配置
## Hive运行日志信息配置
1. Hive的log默认存放在/tmp/user/hive.log目录下（当前用户名下）
2. 修改hive的log存放日志到/opt/module/hive/logs
	1. 修改/opt/module/hive/conf/hive-log4j.properties.template文件名称为hive-log4j.properties
	2.  在hive-log4j.properties文件中修改log存放位置:`hive.log.dir=/opt/module/hive/logs`

## 参数配置方式
1. 查看当前所有的配置信息`hive>set;`
2. 参数的配置三种方式
	1. 配置文件方式:
		1. 默认配置文件：hive-default.xml
		2. 用户自定义配置文件：hive-site.xml
	注意：用户自定义配置会覆盖默认配置。另外，Hive也会读入Hadoop的配置，因为Hive是作为Hadoop的客户端启动的，Hive的配置会覆盖Hadoop的配置。配置文件的设定对本机启动的所有Hive进程都有效。
	
	2. 命令行参数方式:启动Hive时，可以在命令行添加-hiveconf param=value来设定参数。
		例如：`$ bin/hive -hiveconf mapred.reduce.tasks=10;`
		注意：仅对本次hive启动有效
		查看参数设置：`hive (default)> set mapred.reduce.tasks;`
	3. 参数声明方式
		可以在HQL中使用SET关键字设定参数`hive (default)> set mapred.reduce.tasks=100;`注意：仅对本次hive启动有效。
		查看参数设置:`hive (default)> set mapred.reduce.tasks;`
	
	上述三种设定方式的优先级依次递增。即配置文件<命令行参数<参数声明。注意某些系统级的参数，例如log4j相关的设定，必须用前两种方式设定，因为那些参数的读取在会话建立以前已经完成了。
		