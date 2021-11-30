---
Create: 2021年 十一月 30日, 星期二 13:03
tags: 
  - Engineering/MySql
  - 大数据
---

# 插入

## 使用values的方式插入



语法：

```mysql
insert into 表名(字段名，...) values(值1，...);
```

> 特点：
>
> 1. 字段类型和值类型一致或兼容，而且一一对应
> 2. 可以为空的字段，可以不用插入值，或用null填充
> 3. 不可以为空的字段，必须插入值
> 4. 字段个数和值的个数必须一致
> 5. 字段可以省略，但默认所有字段，并且顺序和表中的存储顺序一致

```mysql
#1.插入的值的类型要与列的类型一致或兼容
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

#2.不可以为null的列必须插入值。可以为null的列如何插入值？
INSERT INTO beauty(id,NAME,sex,borndate,phone,photo,boyfriend_id)
VALUES(13,'唐艺昕','女','1990-4-23','1898888888',NULL,2);

#3.列的顺序可以调换
INSERT INTO beauty(NAME,sex,id,phone) VALUES('蒋欣','女',16,'110');

#4.列数和值的个数必须一致
INSERT INTO beauty(NAME,sex,id,phone) VALUES('关晓彤','女',17,'110');

#5.可以省略列名，默认所有列，而且列的顺序和表中列的顺序一致
INSERT INTO beauty VALUES(18,'张飞','男',NULL,'119',NULL,NULL);
```



## 使用set来插入

语法：

```mysql
insert into 表名
set 列名=值,列名=值,...
```

```mysql
INSERT INTO beauty
SET id=19,NAME='刘涛',phone='999';
```



## 比较

```mysql
#1、values支持插入多行,set不支持

INSERT INTO beauty
VALUES(23,'唐艺昕1','女','1990-4-23','1898888888',NULL,2)
,(24,'唐艺昕2','女','1990-4-23','1898888888',NULL,2)
,(25,'唐艺昕3','女','1990-4-23','1898888888',NULL,2);

#2、values支持子查询，set不支持

INSERT INTO beauty(id,NAME,phone)
SELECT 26,'宋茜','11809866';

INSERT INTO beauty(id,NAME,phone)
SELECT id,boyname,'1234567'
FROM boys WHERE id<3;
```







