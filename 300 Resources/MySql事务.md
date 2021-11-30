---
Create: 2021年 十一月 30日, 星期二 13:18
tags: 
  - Engineering/MySql
  - 大数据
---

# 数据库事务

含义：通过一组逻辑操作单元（一组DML——sql语句），将数据从一种状态切换到另外一种状态。

定义：一个或一组sql语句组成一个执行单元，这个执行单元要么全部执行，要么全部不执行。

特点：（ACID）

1. 原子性：要么都执行，要么都回滚
2. 一致性：保证数据的状态操作前和操作后保持一致
3. 隔离性：多个事务同时操作相同数据库的同一个数据时，一个事务的执行不受另外一个事务的干扰
4. 持久性：一个事务一旦提交，则数据将持久化到本地，除非其他事务对其进行修改

相关步骤：

1. 开启事务
2. 编写事务的一组逻辑操作单元（多条sql语句）
3. 提交事务或回滚事务

## 事务的分类：

1. 隐式事务，没有明显的开启和结束事务的标志，比如：insert、update、delete语句本身就是一个事务
2. 显式事务，具有明显的开启和结束事务的标志
	1. 开启事务：取消自动提交事务的功能（set autocommit=0;）
	2. 编写事务的一组逻辑操作单元（多条sql语句）：insert，update，delete
	3. 提交事务或回滚事务

语法：

```mysql
步骤1：开启事务
set autocommit=0;
start transaction;
步骤2：编写事务中的sql语句
(select insert update delete)
语句1;
语句2;

步骤3：结束事务
commit;  提交事务
rollback;  回滚事务

savepoint  节点名;设置保存点
commit to 断点
rollback to 断点
```

```mysql
#开启事务
SET autocommit=0;
START TRANSACTION;
#编写一组事务的语句
UPDATE account SET balance = 1000 WHERE username='张无忌';
UPDATE account SET balance = 1000 WHERE username='赵敏';

#结束事务
ROLLBACK;
#commit;

#2.演示事务对于delete和truncate的处理的区别

SET autocommit=0;
START TRANSACTION;

DELETE FROM account;
ROLLBACK;


#3.演示savepoint 的使用
SET autocommit=0;
START TRANSACTION;
DELETE FROM account WHERE id=25;
SAVEPOINT a;#设置保存点
DELETE FROM account WHERE id=28;
ROLLBACK TO a;#回滚到保存点

```



## 事务的隔离级别:

问题1：事务并发问题如何发生？

当多个事务同时操作同一个数据库的相同数据时



问题2：事务的并发问题有哪些？

脏读：一个事务读取到了另外一个事务未提交的数据
不可重复读：同一个事务中，多次读取到的数据不一致
幻读：一个事务读取数据时，另外一个事务进行更新，导致第一个事务读取到了没有更新的数据



问题3：如何避免事务的并发问题？

通过设置事务的隔离级别

1. READ UNCOMMITTED
2. READ COMMITTED 可以避免脏读
3. REPEATABLE READ 可以避免脏读、不可重复读和一部分幻读
4. SERIALIZABLE可以避免脏读、不可重复读和幻读

| 事务的隔离级别   | 脏读 | 不可重复读 | 幻读 |
| ---------------- | ---- | ---------- | ---- |
| read uncommitted | √    | √          | √    |
| read committed   | ×    | √          | √    |
| repeatable read  | ×    | ×          | √    |
| serializable     | ×    | ×          | ×    |

> mysql中默认 第三个隔离级别 repeatable read

设置隔离级别：

	set session|global  transaction isolation level 隔离级别名;

查看隔离级别：

	select @@tx_isolation;






