---
Create: 2021年 十二月 14日, 星期二 13:23
tags: 
  - Engineering/redis
  - 大数据
---
## 概述
主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主。


## 作用
1. 读写分离
2. 容灾恢复

## 使用
1. 配从(库)不配主(库)
2. 从库配置： slaveof 主库ip 主库端口 。每次与master断开之后，都需要重新连接，除非你配置进redis.conf文件。使用Info replication 来查看主从库信息
3. 修改配置文件
	1. 拷贝多个redis.conf文件 ![[700 Attachments/720 pictures/Pasted image 20211214132746.png]]
	2. 开启daemonize yes ![[700 Attachments/720 pictures/Pasted image 20211214132826.png]]
	3. Pid文件名字
	4. 指定端口
	5. Log文件名字 ![[700 Attachments/720 pictures/Pasted image 20211214132913.png]]
	6. Dump.rdb名字 ![[700 Attachments/720 pictures/Pasted image 20211214132934.png]]


### 常用配置方案
#### 一主二仆
1. Init： ![[700 Attachments/720 pictures/Pasted image 20211214133053.png]]
2. 一个Master两个Slave  ![[700 Attachments/720 pictures/Pasted image 20211214133136.png]]
3. 日志查看
	1. 主机日志  ![[700 Attachments/720 pictures/Pasted image 20211214133215.png]]
	2. 备机日志  ![[700 Attachments/720 pictures/Pasted image 20211214133251.png]]
	3.  info replication   ![[700 Attachments/720 pictures/Pasted image 20211214133319.png]]
	
#### 薪火相传
上一个Slave可以是下一个slave的Master，Slave同样可以接收其他 Slaves的连接和同步请求，那么该slave作为了链条中下一个的master, 可以有效减轻master的写压力，中途变更转向:会清除之前的数据，重新建立拷贝最新的
命令： Slaveof 新主库IP 新主库端口

#### 反客为主
命令：SLAVEOF no one 
使当前数据库停止与其他数据库的同步，转成主数据库


## 复制原理
Slave启动成功连接到master后会发送一个sync命令，Master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令， 在后台进程执行完毕之后，master将传送整个数据文件到slave,以完成一次完全同步。
全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中。
增量复制：Master继续将新的所有收集到的修改命令依次传给slave,完成同步
但是只要是重新连接master,一次完全同步（全量复制)将被自动执行




## 缺点
由于所有的写操作都是先在Master上操作，然后同步更新到Slave上，所以从Master同步到Slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，Slave机器数量的增加也会使这个问题更加严重。