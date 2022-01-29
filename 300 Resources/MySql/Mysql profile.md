---
Create: 2021年 十二月 28日, 星期二 10:07
tags: 
  - Engineering/MySql
  - 大数据
---
## 简介
利用show profile可以查看sql的执行周期！

## 开启profile

查看profile是否开启：
```sql
show variables like '%profiling%'
```
![[700 Attachments/Pasted image 20211228100835.png]]

如果没有开启，可以执行
```sql
set profiling=1
```


## 使用profile
执行show profiles命令，可以查看最近的几次查询。
```sql
show profiles;
```
![[700 Attachments/Pasted image 20211228100943.png]]

根据Query_ID,可以进一步执行show profile cpu,block io for query Query_id来查看sql的具体执行步骤。
![[700 Attachments/Pasted image 20211228101018.png]]

## 查询流程介绍
mysql的查询流程大致是：
1. mysql客户端通过协议与mysql服务器建连接，发送查询语句，先检查查询缓存，如果命中，直接返回结果，否则进行语句解析,也就是说，在解析查询之前，服务器会先访问查询缓存(query cache)——它存储SELECT语句以及相应的查询结果集。如果某个查询结果已经位于缓存中，服务器就不会再对查询进行解析、优化、以及执行。它仅仅将缓存中的结果返回给用户即可，这将大大提高系统的性能。
2. 语法解析器和预处理：首先mysql通过关键字将SQL语句进行解析，并生成一颗对应的“解析树”。mysql解析器将使用mysql语法规则验证和解析查询；预处理器则根据一些mysql规则进一步检查解析数是否合法。
3. 查询优化器当解析树被认为是合法的了，并且由优化器将其转化成执行计划。一条查询可以有很多种执行方式，最后都返回相同的结果。优化器的作用就是找到这其中最好的执行计划。。
4. 然后，mysql默认使用的BTREE索引，并且一个大致方向是:无论怎么折腾sql，至少在目前来说，mysql最多只用到表中的一个索引。

## SQL的执行顺序
手写的顺序：
![[700 Attachments/Pasted image 20211228101247.png]]
真正执行的顺序：随着Mysql版本的更新换代，其优化器也在不断的升级，优化器会分析不同执行顺序产生的性能消耗不同而动态调整执行顺序。下面是经常出现的查询顺序：
![[700 Attachments/Pasted image 20211228101329.png]]

![[700 Attachments/Pasted image 20211228101352.png]]