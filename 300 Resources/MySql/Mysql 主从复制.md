---
Create: 2021年 十二月 28日, 星期二 13:13
tags: 
  - Engineering/MySql
  - 大数据
---

## 基本原理
默认binlog文件会保存在/var/lib/mysql目录中，以mysql-bin.xxxx开头。可以使用工具mysqlbinlog进行查看。

mysql的主从复制为设置一台mysql服务实例为主机，其他一台为从机。主机的数据可以实时同步到从机。

MySQL复制过程分成三步：
1. master将改变记录到二进制日志（binary log）。这些记录过程叫做二进制日志事件，binary log events
2. slave将master的binary log events拷贝到它的中继日志（relay log）
3. slave重做中继日志中的事件，将改变应用到自己的数据库中。 MySQL复制是异步的且串行化的

![[700 Attachments/Pasted image 20211228131546.png]]

## 基本原则
1. 每个slave只有一个master
2. 每个slave只能有一个唯一的服务器ID
3. 每个master可以有多个salve



## 一主一从配置
### 基本要求
1.	mysql版本一致且后台以服务运行
2.	确保主机和从机都开启了二进制日志
3.	主从都配置在[mysqld]结点下，都是小写


## 配置步骤
1. 修改主机的my.cnf配置文件
	```
	#主服务器的id
	server-id=1
	#启用二进制日志
	log-bin=mysql-bin
	#设置不复制的数据库(选配)
	binlog-ignore-db=mysql
	#设置要复制的数据库(选配)
	binlog-do-db=需要复制的主数据库名字（设置一个之前没有的数据库）
	#设置logbin的格式
	binlog_format=mixed
	```
2. 修改从机的my.cnf配置文件
	```
	server-id = 2
	```
	
3. 主机和从机都重启服务
4. 主机和从机都关闭防火墙
5. 在主机上建立账户授权从机
	```
	GRANT replication slave ON *.* TO 'slave'@'%' IDENTIFIED BY '123456';
	```
6. 查询master的状态，记下File和Position的值,在主机执行show master status查看主机的binlog日志信息
	![[700 Attachments/Pasted image 20211228132159.png]]
	执行完此步骤后不要再操作主服务器MySQL，防止主服务器状态值变化
7. 在从机上配置需要复制的主机:使用MySQL客户端连接从机服务，如果之前配置过主从，需要先执行stop slave停止主从关系。之后输入以下命令：
	```
	CHANGE MASTER TO MASTER_HOST='主机IP',
	MASTER_USER='slave',
	MASTER_PASSWORD='123456',
	MASTER_LOG_FILE='File名字',
	MASTER_LOG_POS=Position数字;
	```
	配置完成后，在从机的/var/lib/mysql目录下会产生一个名为master.info的主机配置信息文件。
8. 启动从服务器复制功能
	```
	start slave;
	```
9. 显示从机状态
	```
	show slave status;
	```

10. 下面两个参数都是Yes，则说明主从配置成功！
	```
	Slave_IO_Running: Yes
	Slave_SQL_Running: Yes
	```
	
11. 主机新建库、新建表、insert记录，从机复制
12. 停止从服务的复制功能
	```
	stop slave;
	```
