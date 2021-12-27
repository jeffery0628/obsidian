---
Create: 2021年 十一月 30日, 星期二 09:56
tags: 
  - Engineering/MySql
  - 大数据
---
# 分页查询

实际的web项目中需要根据用户的需求提交对应的分页查询的sql语句

语法：

```mysql
select 字段|表达式,...
from 表
【where 条件】
【group by 分组字段】
【having 条件】
【order by 排序的字段】
limit 【起始的条目索引，】条目数;
```

> 特点：
>
> 1. 起始条目索引从0开始
> 2. limit子句放在查询语句的最后
> 3. 公式：select * from  表 limit （page-1）\*sizePerPage,  sizePerPage

```mysql
#1：查询前五条员工信息
SELECT * FROM  employees LIMIT 0,5;
SELECT * FROM  employees LIMIT 5;

#2：查询第11条——第25条
SELECT * FROM  employees LIMIT 10,15;
#案例3：有奖金的员工信息，并且工资较高的前10名显示出来
SELECT * 
FROM employees 
WHERE commission_pct IS NOT NULL 
ORDER BY salary DESC 
LIMIT 10 ;
```






