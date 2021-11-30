---
Create: 2021年 十一月 29日, 星期一 23:22
tags: 
  - Engineering/MySql
  - 大数据

---
# 基础查询

语法：

```mysql
SELECT 要查询的东西
FROM 表名;
```

> ①通过select查询完的结果 ，是一个虚拟的表格，不是真实存在
>
> ②要查询的东西 可以是常量值、可以是表达式、可以是字段、可以是函数

## 查询表中字段

```mysql
#1.查询表中单个字段
SELECT last_name FROM employees;
#2.查询表中的多个字段
SELECT last_name,salary,email FROM employees;
#3.查询表中的所有字段
#方式一：
SELECT 
    `employee_id`,
    `first_name`,
    `last_name`,
    `phone_number`,
    `last_name`,
    `job_id`,
    `phone_number`,
    `job_id`,
    `salary`,
    `commission_pct`,
    `manager_id`,
    `department_id`,
    `hiredate` 
FROM
    employees ;
    
#方式二：  
SELECT * FROM employees;
 
#查询员工号，姓名，工资，以及工资提高百分之20%后的结果（new salary）
SELECT employee_id,last_name,salary,salary*1.2 "new salary"
FROM employees;

#将员工的姓名按首字母排序，并写出姓名的长度（length）
SELECT LENGTH(last_name) 长度,SUBSTR(last_name,1,1) 首字符,last_name
FROM employees
ORDER BY 首字符;

#查询员工最高工资和最低工资的差距（DIFFERENCE）
SELECT MAX(salary)-MIN(salary) DIFFRENCE
FROM employees;
```

## 查询常量

```mysql
#4.查询常量值
SELECT 100;
SELECT 'john';
SELECT NOW();
```

## 查询表达式

```mysql
#5.查询表达式
SELECT 100%98;
```

## 查询函数

```mysql
#6.查询函数
SELECT VERSION();
```

## 起别名

```mysql
#7.起别名
#方式一：使用as
SELECT 100%98 AS 结果;
SELECT last_name AS 姓,first_name AS 名 FROM employees;
#方式二：使用空格
SELECT last_name 姓,first_name 名 FROM employees;
#案例：查询salary，显示结果为 out put
SELECT salary AS "out put" FROM employees;
```

> 起别名的好处：①便于理解②如果要查询的字段有重名的情况，使用别名可以区分开来

## 去重

```mysql
#案例：查询员工表中涉及到的所有的部门编号
SELECT DISTINCT department_id FROM employees;
```

## +号的作用

```mysql
1. 两个操作数都为数值型，则做加法运算
select 100+90; 
2. 只要其中一方为字符型，试图将字符型数值转换成数值型
select '123'+90;  	如果转换成功，则继续做加法运算
select 'john'+90;	如果转换失败，则将字符型数值转换成0
select null+10; 	只要其中一方为null，则结果肯定为null
```







