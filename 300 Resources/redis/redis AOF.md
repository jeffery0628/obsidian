---
Create: 2021年 十二月 13日, 星期一 23:07
tags: 
  - Engineering/redis
  - 大数据
---


## AOF 是什么
以日志的形式来记录每个写操作，将Redis执行过的所有写指令记录下来(读操作不记录)，只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。
Aof保存的是appendonly.aof文件


## 配置文件中位置
appendfilename "appendonly.aof"


## AOF 启动/恢复

### 启动
配置文件redis.conf，修改默认的appendonly no，改为yes
### 正常恢复
1. 将有数据的aof文件复制一份保存到对应目录(config get dir)
2. 恢复：重启redis然后重新加载

### 异常恢复
1. redis-check-aof --fix进行修复
2. 恢复：重启redis然后重新加载



## rewrite
AOF采用文件追加方式，文件会越来越大为避免出现此种情况，新增了重写机制,当AOF文件的大小超过所设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集.可以使用命令bgrewriteaof

### 原理
AOF文件持续增长而过大时，会fork出一条新进程来将文件重写(也是先写临时文件最后再rename)，遍历新进程的内存中数据，每条记录有一条的Set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似。
### 触发机制
Redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

## 优点
1. 每修改同步：appendfsync always   同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好
2. 每秒同步：appendfsync everysec    异步操作，每秒记录   如果一秒内宕机，有数据丢失
3. 不同步：appendfsync no   从不同步


## 缺点
1. 相同数据集的数据而言aof文件要远大于rdb文件，恢复速度慢于rdb
2. aof运行效率要慢于rdb,每秒同步策略效率较好，不同步效率和rdb相同







