---
Create: 2021年 十二月 13日, 星期一 09:43
tags: 
  - Engineering/redis
  - 大数据
---

## 配置文件位置
配置文件放在安装目录下(/opt/redis-5.0.14/redis.conf)，需拷贝出来进行配置(/myconf)

## 常见配置redis.conf介绍
参数说明redis.conf 配置项说明如下：
1. Redis默认不是以守护进程的方式运行，可以通过该配置项修改，使用yes启用守护进程  daemonize no
2. 当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定  pidfile /var/run/redis.pid
3. 指定Redis监听端口，默认端口为6379，作者在自己的一篇博文中解释了为什么选用6379作为默认端口，因为6379在手机按键上MERZ对应的号码，而MERZ取自意大利歌女Alessia Merz的名字  port 6379
4. 绑定的主机地址  bind 127.0.0.1
5. 当 客户端闲置多长时间后关闭连接，如果指定为0，表示关闭该功能  timeout 300
6.  指定日志记录级别，Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose  loglevel verbose
7.  日志记录方式，默认为标准输出，如果配置Redis为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给/dev/null  logfile stdout
8.  设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id  databases 16
9.  指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合  save    Redis默认配置文件中提供了三个条件：  save 900 1  save 300 10  save 60 10000  分别表示900秒（15分钟）内有1个更改，300秒（5分钟）内有10个更改以及60秒内有10000个更改。 
10.  指定存储至本地数据库时是否压缩数据，默认为yes，Redis采用LZF压缩，如果为了节省CPU时间，可以关闭该选项，但会导致数据库文件变的巨大  rdbcompression yes
11.  指定本地数据库文件名，默认值为dump.rdb  dbfilename dump.rdb
12.  指定本地数据库存放目录  dir ./
13.   设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步  slaveof  
14.   当master服务设置了密码保护时，slav服务连接master的密码  masterauth 
15.   设置Redis连接密码，如果配置了连接密码，客户端在连接Redis时需要通过AUTH 命令提供密码，默认关闭  requirepass foobared
16.   设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息  maxclients 128
17.   指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis新的vm机制，会把Key存放内存，Value会存放在swap区  maxmemory 
18.   指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis本身同步数据文件是按上面save条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为no  appendonly no
19.   指定更新日志文件名，默认为appendonly.aof   appendfilename appendonly.aof
20.   指定更新日志条件，共有3个可选值：   no：表示等操作系统进行数据缓存同步到磁盘（快）   always：表示每次更新操作后手动调用fsync()将数据写到磁盘（慢，安全）   everysec：表示每秒同步一次（折衷，默认值）  appendfsync everysec 
21.   指定是否启用虚拟内存机制，默认值为no，简单的介绍一下，VM机制将数据分页存放，由Redis将访问量较少的页即冷数据swap到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析Redis的VM机制）   vm-enabled no
22.   虚拟内存文件路径，默认值为/tmp/redis.swap，不可多个Redis实例共享   vm-swap-file /tmp/redis.swap
23.   将所有大于vm-max-memory的数据存入虚拟内存,无论vm-max-memory设置多小,所有索引数据都是内存存储的(Redis的索引数据 就是keys),也就是说,当vm-max-memory设置为0的时候,其实是所有value都存在于磁盘。默认值为0   vm-max-memory 0
24.   Redis swap文件分成了很多的page，一个对象可以保存在多个page上面，但一个page上不能被多个对象共享，vm-page-size是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page大小最好设置为32或者64bytes；如果存储很大大对象，则可以使用更大的page，如果不 确定，就使用默认值   vm-page-size 32
25.   设置swap文件中的page数量，由于页表（一种表示页面空闲或使用的bitmap）是在放在内存中的，，在磁盘上每8个pages将消耗1byte的内存。   vm-pages 134217728
26.   设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4   vm-max-threads 4
27.   设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启  glueoutputbuf yes
28.   指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法  hash-max-zipmap-entries 64  hash-max-zipmap-value 512
29.   指定是否激活重置哈希，默认为开启（后面在介绍Redis的哈希算法时具体介绍）  activerehashing yes
30.   指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件  include /path/to/local.conf


## INCLUDES
使用该模块的redis.conf可以作为管理其他服务器上redis的总闸开关，或者将redis配置模块化管理。
includes 即为将其他配置文件包含进来，下文相同配置，可对包含进的配置文件进行覆盖。


## GENERAL
1. daemonize: 默认情况下，Redis不作为守护程序运行。如果你需要，修改为yes。当使用守护进程时，Redis将在/var/run/redis.pid中写入一个pid文件
2. supervised： 
3. pidfile /var/run/redis_6379.pid
4. loglevel notice
5. logfile ""
6. syslog-enabled no: 是否把日志输出到syslog中
7. syslog-ident redis:指定syslog里的日志标志
8. syslog-facility local0:
9. databases 16: 默认数据库个数，16个
10. always-show-logo yes：启动时是否显示logo


## SNAPSHOTTING
RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照触发条件，默认是1分钟内改了1万次，或5分钟内改了10次，或15分钟内改了1次。
save 900 1
save 300 10
save 60 10000
1. stop-writes-on-bgsave-error yes： 使用RDB来快照时，如果后台保存数据失败，redis将停止写入数据（这能使用户意识到数据没有被正确保存），如果配置成no，表示不在乎数据不一致或者有其他的手段发现和控制数据的不一致性。
2. rdbcompression yes：rdbcompression：对于存储到磁盘中的快照，可以设置是否进行压缩存储。如果是的话，redis会采用LZF算法进行压缩。如果你不想消耗CPU来进行压缩的话，可以设置为关闭此功能。
3. rdbchecksum yes： 在存储快照后，还可以让redis使用CRC64算法来进行数据校验，但是这样做会增加大约10%的性能消耗，如果希望获取到最大的性能提升，可以关闭此功能
4. dbfilename dump.rdb ： 快照文件名称
5. dir ./ : 快照文件保存路径，默认是在安装目录下 /usr/local/bin


## REPLICATION 主从复制
主从复制，常用于读写分离。使用replicaof使一个Redis实例成为另一个Redis服务器的副本。
1. redis的主从复制是异步的
2. 如果复制链路在相对较短的时间内丢失，Redis副本机器能够与主服务器自动重新同步。
3. 复制是自动的，不需要用户干预。网络分区后，副本会自动尝试重新连接到主服务器并与之重新同步。

---
1. replicaof \<masterip> \<masterport>
2. masterauth \<master-password>   ,如果主机配置了密码，使用主从复制时，可在此配置密码
3. replica-serve-stale-data yes ： 如果副本机正在进行主从复制，yes：表示对于读请求，副本机仍然会进行答复，这时可能会存在数据的不一致性，no：会进行报错：SYNC with master in progres，表示副本机正在与主机同步
4. replica-read-only yes ： 副本用于只读（读写分离），副本如果进行写入操作的话，写入的数据并不会同步到主机上。
5. repl-diskless-sync no: 是否进行无盘复制，在高带宽，低性能硬盘的情况下，无盘复制会更好
6. repl-diskless-sync-delay 5 ： 当无盘复制开启时，


## SECURITY
访问密码的查看、设置、取消

## CLIENTS
maxclients 10000 : 连接redis服务器的客户端最大数量：10000,如果达到了此限制，redis则会拒绝新的连接请求，并且向这些连接请求方发出“max number of clients reached”以作回应。


## maxmemory
1. maxmemory: 设置redis可以使用的内存量。一旦到达内存使用上限，redis将会试图移除内部数据，移除规则可以通过maxmemory-policy来指定。如果redis无法根据移除规则来移除内存中的数据，或者设置了“不允许移除”，那么redis则会针对那些需要申请内存的指令返回错误信息，比如SET、LPUSH等。但是对于无内存申请的指令，仍然会正常响应，比如GET等。如果你的redis是主redis（说明你的redis有从redis），那么在设置内存使用上限时，需要在系统中留出一些内存空间给同步队列缓存，只有在你设置的是“不移除”的情况下，才不用考虑这个因素
2. maxmemory-policy 
	1. volatile-lru: 使用LRU算法来移除带有过期的key
	2. allkeys-lru: 针对所有的key ，使用LRU算法来移除key
	3. volatile-random: 对带有过期的key的集合中，随机移除key
	4. allkeys-random:随机的移除任何一个key
	5. volatile-ttl: 根据最近到期时间来移除key
	6. noeviction ： 永不过期，只会对插入数据返回一个error

3. maxmemory-samples: 设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以你可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个

## APPEND ONLY MODE(追加)
1. appendonly
2. appendfilename
3. appendfsync
	1. always：同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好
	2. everysec：出厂默认推荐，异步操作，每秒记录   如果一秒内宕机，有数据丢失
	3. no

4. no-appendfsync-on-rewrite：重写时是否可以运用Appendfsync，用默认no即可，保证数据安全性。
5. auto-aof-rewrite-min-size：设置重写的基准值
6. auto-aof-rewrite-percentage：设置重写的基准值


