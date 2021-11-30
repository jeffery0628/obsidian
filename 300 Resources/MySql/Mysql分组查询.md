---
Create: 2021年 十一月 30日, 星期二 09:24
tags: 
  - Engineering/MySql
  - 大数据
---
# 分组查询

语法：

```mysql
select 查询的字段，分组函数
from 表
group by 分组的字段
```

 特点：

 1. 可以按单个字段分组
 2. 和分组函数一同查询的字段最好是分组后的字段
 3. 可以按多个字段分组，字段之间用逗号隔开
 5. 可以支持排序
 6. having后可以支持别名

## 简单分组

```mysql
#案例1：查询每个工种的员工平均工资
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id;
#案例2：查询每个位置的部门个数
SELECT COUNT(*),location_id
FROM departments
GROUP BY location_id;

#查询具有各个job_id的员工人数
SELECT COUNT(*) 个数,job_id
FROM employees
GROUP BY job_id;
```

## 分组前筛选

```mysql
#案例1：查询邮箱中包含a字符的每个部门的最高工资
SELECT MAX(salary),department_id
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;

#案例2：查询有奖金的每个领导手下员工的平均工资
SELECT AVG(salary),manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;
```

## 分组后筛选

```mysql
#案例：查询哪个部门的员工个数>5
#①查询每个部门的员工个数
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id;

#② 筛选刚才①结果
SELECT COUNT(*),department_id
FROM employees
GROUP BY department_id
HAVING COUNT(*)>5;

#案例2：每个工种有奖金的员工的最高工资>12000的工种编号和最高工资
SELECT job_id,MAX(salary)
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary)>12000;

#案例3：领导编号>102的每个领导手下的最低工资大于5000的领导编号和最低工资 
SELECT manager_id,MIN(salary)
FROM employees
WHERE manager_id>102
GROUP BY manager_id
HAVING MIN(salary)>5000;

#查询各个管理者手下员工的最低工资，其中最低工资不能低于6000，没有管理者的员工不计算在内
SELECT MIN(salary),manager_id
FROM employees
WHERE manager_id IS NOT NULL
GROUP BY manager_id
HAVING MIN(salary)>=6000;
```

## 分组并排序

```mysql
#案例：每个工种有奖金的员工的最高工资>6000的工种编号和最高工资,按最高工资升序
SELECT job_id,MAX(salary) m
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING m>6000
ORDER BY m ;

#查询各job_id的员工工资的最大值，最小值，平均值，总和，并按job_id升序
SELECT MAX(salary),MIN(salary),AVG(salary),SUM(salary),job_id
FROM employees
GROUP BY job_id
ORDER BY job_id;

#查询所有部门的编号，员工数量和工资平均值,并按平均工资降序
SELECT department_id,COUNT(*),AVG(salary) a
FROM employees
GROUP BY department_id
ORDER BY a DESC;
```

## 按多个字段分组

```mysql
#案例：查询每个工种每个部门的最低工资,并按最低工资降序
SELECT MIN(salary),job_id,department_id
FROM employees
GROUP BY department_id,job_id
ORDER BY MIN(salary) DESC;
```






