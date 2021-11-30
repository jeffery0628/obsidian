---
Create: 2021年 十一月 30日, 星期二 10:00
tags: 
  - Engineering/MySql
  - 大数据
---



# 联合查询

```mysql
select 字段|常量|表达式|函数 【from 表】 【where 条件】 
union 【all】
select 字段|常量|表达式|函数 【from 表】 【where 条件】 
union 【all】
.....
select 字段|常量|表达式|函数 【from 表】 【where 条件】
```

> 特点:
>
> 1. 多条查询语句的查询的列数必须是一致的
> 2. 多条查询语句的查询的列的类型几乎相同
> 3. union代表去重，union all代表不去重

```mysql
#1：查询部门编号>90或邮箱包含a的员工信息
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90;;

SELECT * FROM employees  WHERE email LIKE '%a%'
UNION
SELECT * FROM employees  WHERE department_id>90;


#2：查询中国用户中男性的信息以及外国用户中年男性的用户信息

SELECT id,cname FROM t_ca WHERE csex='男'
UNION ALL
SELECT t_id,tname FROM t_ua WHERE tGender='male';


```



