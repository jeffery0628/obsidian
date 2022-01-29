---
Create: 2021年 十二月 31日, 星期五 21:25
tags: 
  - Engineering/MySql
  - 大数据
---
## 慢查询日志
### 是什么
MySQL的慢查询日志是MySQL提供的一种日志记录，它用来记录在MySQL中响应时间超过阈值的语句，具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。
具体指运行时间超过long_query_time值的SQL，则会被记录到慢查询日志中。long_query_time的默认值为10，意思是运行10秒以上的语句

### 怎么用？
默认情况下，MySQL数据库没有开启慢查询日志，需要我们手动来设置这个参数。当然，如果不是调优需要的话，一般不建议启动该参数，因为开启慢查询日志会或多或少带来一定的性能影响。慢查询日志支持将日志记录写入文件。
查看是否开启：
```sql
SHOW VARIABLES LIKE '%slow_query_log%';
```
开启：
```sql
set global slow_query_log=1;
```
使用set global slow_query_log=1;开启了慢查询日志只对当前数据库生效，如果MySQL重启后则会失效。如果要永久生效，就必须修改配置文件my.cnf（其它系统变量也是如此）
查询慢查询日志的阈值:
```sql
SHOW VARIABLES LIKE 'long_query_time%';
```
设置阈值：
```sql
set long_query_time=3
```
查询当前系统中有多少条慢查询记录：
```sql
show global status like '%Slow_queries%';
```
永久开启:修改my.cnf文件，在\[mysqld\]下增加或修改参数:
```sql
slow_query_log =1
slow_query_log_file=/var/lib/mysql/hadoop100-slow.log
long_query_time=3;
log_output=FILE

```
关于慢查询的参数slow_query_log_file ，它指定慢查询日志文件的存放路径，系统默认会给一个缺省的文件host_name-slow.log（如果没有指定参数slow_query_log_file的话）

## 日志分析工具：mysqldumpslow
在生产环境中，如果要手工分析日志，查找、分析SQL，显然是个体力活，MySQL提供了日志分析工具mysqldumpslow。
查看mysqldumpslow的帮助信息：在Linux中输入命令mysqldumpslow –help
1. -s: 是表示按照何种方式排序
	1. c: 访问次数
	2. l: 锁定时间
	3. r: 返回记录
	4. t: 查询时间
	5. al:平均锁定时间
	6. ar:平均返回记录数
	7. at:平均查询时间
2. -t:即为返回前面多少条的数据
3. -g:后边搭配一个正则匹配模式，大小写不敏感的


### 工作常用参考
得到返回次数最多的10个SQL：
```sql
mysqldumpslow -s r -t 10 /var/lib/mysql/hadoop100-slow.log
```

得到访问次数最多的10个SQL：
```sql
mysqldumpslow -s c -t 10 /var/lib/mysql/hadoop100-slow.log
```

得到按照时间排序的前10条里面含有左连接的查询语句
```sql
mysqldumpslow -s t -t 10 -g "left join" /var/lib/mysql/hadoop100-slow.log
```

另外建议在使用这些命令时结合 | 和more 使用 ，否则有可能出现爆屏情况
```sql
mysqldumpslow -s r -t 10 /var/lib/mysql/hadoop100-slow.log | more
```

