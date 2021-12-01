---
Create: 2021年 十一月 30日, 星期二 09:23
tags: 
  - Engineering/MySql
  - 大数据
---
# 排序查询

语法：

```mysql
select
	要查询的东西
from
	表
where 
	条件
order by 排序的字段|表达式|函数|别名 【asc|desc】
```

> 1. asc代表的是升序，可以省略；desc代表的是降序
> 2. order by子句可以支持 单个字段、别名、表达式、函数、多个字段
> 3. order by子句在查询语句的最后面，除了limit子句



## 按单个字段排序

```mysql
#案例1：查询员工信息，并按照薪资降序排序
SELECT * 
FROM employees 
ORDER BY salary DESC;
```

## 添加筛选条件再排序

```mysql
#案例：查询部门编号>=90的员工信息，并按员工编号降序
SELECT *
FROM employees
WHERE department_id>=90
ORDER BY employee_id DESC;

#选择工资不在8000到17000的员工的姓名和工资，按工资降序
SELECT last_name,salary
FROM employees
WHERE salary NOT BETWEEN 8000 AND 17000
ORDER BY salary DESC;
```

## 按表达式排序

```mysql
#案例3：查询员工信息 按年薪降序
SELECT *,salary*12*(1+IFNULL(commission_pct,0))
FROM employees
ORDER BY salary*12*(1+IFNULL(commission_pct,0)) DESC;
```

## 按别名排序

```mysql
#案例4：查询员工信息 按年薪升序
SELECT *,salary*12*(1+IFNULL(commission_pct,0)) AS 年薪
FROM employees
ORDER BY 年薪 ASC;
```

## 按函数排序

```mysql
#案例：查询员工名，并且按名字的长度降序
SELECT LENGTH(last_name),last_name 
FROM employees
ORDER BY LENGTH(last_name) DESC;

#查询邮箱中包含e的员工信息，并先按邮箱的字节数降序，再按部门号升序
SELECT *,LENGTH(email)
FROM employees
WHERE email LIKE '%e%'
ORDER BY LENGTH(email) DESC,department_id ASC;
```

## 按多个字段排序

```mysql
#案例：查询员工信息，要求先按工资降序，再按employee_id升序
SELECT *
FROM employees
ORDER BY salary DESC,employee_id ASC;

#查询员工的姓名和部门号和年薪，按年薪降序 按姓名升序
SELECT last_name,department_id,salary*12*(1+IFNULL(commission_pct,0)) 年薪
FROM employees
ORDER BY 年薪 DESC,last_name ASC;
```







