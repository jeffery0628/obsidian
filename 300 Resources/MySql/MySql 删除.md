---
Create: 2021年 十一月 30日, 星期二 13:07
tags: 
  - Engineering/MySql
  - 大数据
---

# 删除

## 单表删除：

```mysql
delete from 表名 【where 筛选条件】
```

```mysql
#1：删除手机号以9结尾的女神信息
DELETE FROM beauty 
WHERE phone LIKE '%9';
```



## 多表删除：

```mysql
# sql92
delete 别名1，别名2
from 表1 别名1，表2 别名2
where 连接条件
and 筛选条件;

# sql99
delete 表1的别名,表2的别名
from 表1 别名
inner|left|right join 表2 别名 on 连接条件
where 筛选条件;
```

```mysql
#1：删除张无忌的女朋友的信息
DELETE b
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id` = bo.`id`
WHERE bo.`boyName`='张无忌';

#2：删除黄晓明的信息以及他女朋友的信息
DELETE b,bo
FROM beauty b
INNER JOIN boys bo ON b.`boyfriend_id`=bo.`id`
WHERE bo.`boyName`='黄晓明';
```



## 清空表：

```mysql
truncate table 表名
```

> 1. truncate不能加where条件，而delete可以加where条件
> 2. truncate的效率高一丢丢
> 3. truncate 删除带自增长的列的表后，如果再插入数据，数据从1开始
> 	delete 删除带自增长列的表后，如果再插入数据，数据从上一次的断点处开始
> 4. truncate删除不能回滚，delete删除可以回滚






