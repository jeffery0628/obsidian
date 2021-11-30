---
Create: 2021年 十一月 30日, 星期二 09:39
tags: 
  - Engineering/MySql
  - 大数据
---

# 子查询

一条查询语句中又嵌套了另一条完整的select语句，其中被嵌套的select语句，称为子查询或内查询
在外面的查询语句，称为主查询或外查询



特点：

1. 子查询都放在小括号内

2. 子查询可以放在from后面、select后面、where后面、having后面，但一般放在条件的右侧

3. 子查询优先于主查询执行，主查询使用了子查询的执行结果

4. 子查询根据查询结果的行数不同分为以下两类：

	1. 单行子查询：结果集只有一行，一般搭配单行操作符使用：> < = <> >= <= 
		非法使用子查询的情况：

		1. 子查询的结果为一组值
		2. 子查询的结果为空

	2. 多行子查询：结果集有多行，一般搭配多行操作符使用：any、all、in、not in。

		in： 属于子查询结果中的任意一个就行，any和all往往可以用其他查询代替。

## where 或 having 的后面

> 特点：
>
> ①子查询放在小括号内
>
> ②子查询一般放在条件的右侧
>
> ③标量子查询，一般搭配着单行操作符使用
>
> ④子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果

### 标量子查询（单行子查询）

```mysql
#案例1：谁的工资比 Abel 高?
#①查询Abel的工资
SELECT salary
FROM employees
WHERE last_name = 'Abel'
#②查询员工的信息，满足 salary>①结果
SELECT *
FROM employees
WHERE salary>(

	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'

);

#案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资
#①查询141号员工的job_id
SELECT job_id
FROM employees
WHERE employee_id = 141
#②查询143号员工的salary
SELECT salary
FROM employees
WHERE employee_id = 143
#③查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②
SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143

);


#案例3：返回公司工资最少的员工的last_name,job_id和salary
#①查询公司的 最低工资
SELECT MIN(salary)
FROM employees
#②查询last_name,job_id和salary，要求salary=①
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);


#案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资
#①查询50号部门的最低工资
SELECT  MIN(salary)
FROM employees
WHERE department_id = 50
#②查询每个部门的最低工资
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
#③ 在②基础上筛选，满足min(salary)>①
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50
);

#非法使用标量子查询

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250
);
```

### 列子查询（多行子查询）

```mysql
#案例1：返回location_id是1400或1700的部门中的所有员工姓名
#①查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700)
#②查询员工姓名，要求部门号是①列表中的某一个
SELECT last_name
FROM employees
WHERE department_id  <>ALL(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)
);


#案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary
#①查询job_id为‘IT_PROG’部门任一工资
SELECT DISTINCT salary
FROM employees
WHERE job_id = 'IT_PROG'
#②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'
) AND job_id<>'IT_PROG';
#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';


#案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';
#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';
```

### 行子查询（一行多列或多行多列）

```mysql

#案例：查询员工编号最小并且工资最高的员工信息
SELECT * 
FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);
#①查询最小的员工编号
SELECT MIN(employee_id)
FROM employees
#②查询最高工资
SELECT MAX(salary)
FROM employees
#③查询员工信息
SELECT *
FROM employees
WHERE employee_id=(
	SELECT MIN(employee_id)
	FROM employees
)AND salary=(
	SELECT MAX(salary)
	FROM employees
);

```

## select 后面

仅仅支持标量子查询

```mysql
#案例：查询每个部门的员工个数
SELECT d.*,(

	SELECT COUNT(*)
	FROM employees e
	WHERE e.department_id = d.`department_id`
 ) 个数
FROM departments d;
 
 
#案例2：查询员工号=102的部门名
SELECT (
	SELECT department_name,e.department_id
	FROM departments d
	INNER JOIN employees e
	ON d.department_id=e.department_id
	WHERE e.employee_id=102
	
) 部门名;
```

## from后面

将子查询结果充当一张表，要求必须起别名

```mysql
#案例：查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id


SELECT * FROM job_grades;


#②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal

SELECT  ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;

```

## exists后面（相关子查询）

exists(完整的查询语句) 结果：0 或 1

```mysql
SELECT EXISTS(SELECT employee_id FROM employees WHERE salary=300000);

#案例1：查询有员工的部门名

#in
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees

)

#exists

SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`


);


#案例2：查询没有女朋友的男神信息

#in

SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
)

#exists
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id`=b.`boyfriend_id`

);

```

## 练习

```mysql
# 1. 查询工资最低的员工信息: last_name, salary
#①查询最低的工资
SELECT MIN(salary)
FROM employees
#②查询last_name,salary，要求salary=①
SELECT last_name,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);


# 2. 查询平均工资最低的部门信息
#方式一：
#①各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

#②查询①结果上的最低平均工资
SELECT MIN(ag)
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
#③查询哪个部门的平均工资=②
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary)=(
	SELECT MIN(ag)
	FROM (
		SELECT AVG(salary) ag,department_id
		FROM employees
		GROUP BY department_id
	) ag_dep

);
#④查询部门信息
SELECT d.*
FROM departments d
WHERE d.`department_id`=(
	SELECT department_id
	FROM employees
	GROUP BY department_id
	HAVING AVG(salary)=(
		SELECT MIN(ag)
		FROM (
			SELECT AVG(salary) ag,department_id
			FROM employees
			GROUP BY department_id
		) ag_dep
	)
);

#方式二：
#①各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

#②求出最低平均工资的部门编号
SELECT department_id
FROM employees
GROUP BY department_id
ORDER BY AVG(salary) 
LIMIT 1;

#③查询部门信息
SELECT *
FROM departments
WHERE department_id=(
	SELECT department_id
	FROM employees
	GROUP BY department_id
	ORDER BY AVG(salary) 
	LIMIT 1
);




# 3. 查询平均工资最低的部门信息和该部门的平均工资
#①各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
#②求出最低平均工资的部门编号
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
ORDER BY AVG(salary) 
LIMIT 1;
#③查询部门信息
SELECT d.*,ag
FROM departments d
JOIN (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
	ORDER BY AVG(salary) 
	LIMIT 1

) ag_dep
ON d.`department_id`=ag_dep.department_id;

# 4. 查询平均工资最高的 job 信息
#①查询最高的job的平均工资
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id
ORDER BY AVG(salary) DESC
LIMIT 1

#②查询job信息
SELECT * 
FROM jobs
WHERE job_id=(
	SELECT job_id
	FROM employees
	GROUP BY job_id
	ORDER BY AVG(salary) DESC
	LIMIT 1

);
# 5. 查询平均工资高于公司平均工资的部门有哪些?

#①查询平均工资
SELECT AVG(salary)
FROM employees

#②查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id

#③筛选②结果集，满足平均工资>①

SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary)>(
	SELECT AVG(salary)
	FROM employees

);

# 6. 查询出公司中所有 manager 的详细信息.
#①查询所有manager的员工编号
SELECT DISTINCT manager_id
FROM employees

#②查询详细信息，满足employee_id=①
SELECT *
FROM employees
WHERE employee_id =ANY(
	SELECT DISTINCT manager_id
	FROM employees
);

# 7. 各个部门中 最高工资中最低的那个部门的 最低工资是多少
#①查询各部门的最高工资中最低的部门编号
SELECT department_id
FROM employees
GROUP BY department_id
ORDER BY MAX(salary)
LIMIT 1
#②查询①结果的那个部门的最低工资
SELECT MIN(salary) ,department_id
FROM employees
WHERE department_id=(
	SELECT department_id
	FROM employees
	GROUP BY department_id
	ORDER BY MAX(salary)
	LIMIT 1
);
# 8. 查询平均工资最高的部门的 manager 的详细信息: last_name, department_id, email, salary
#①查询平均工资最高的部门编号
SELECT 
    department_id 
FROM
    employees 
GROUP BY department_id 
ORDER BY AVG(salary) DESC 
LIMIT 1 

#②将employees和departments连接查询，筛选条件是①
    SELECT 
        last_name, d.department_id, email, salary 
    FROM
        employees e 
        INNER JOIN departments d 
            ON d.manager_id = e.employee_id 
    WHERE d.department_id = 
        (SELECT 
            department_id 
        FROM
            employees 
        GROUP BY department_id 
        ORDER BY AVG(salary) DESC 
        LIMIT 1) ;


#1.	查询和Zlotkey相同部门的员工姓名和工资
#①查询Zlotkey的部门
SELECT department_id
FROM employees
WHERE last_name = 'Zlotkey'
#②查询部门号=①的姓名和工资
SELECT last_name,salary
FROM employees
WHERE department_id = (
	SELECT department_id
	FROM employees
	WHERE last_name = 'Zlotkey'
)

#2.查询工资比公司平均工资高的员工的员工号，姓名和工资。
#①查询平均工资
SELECT AVG(salary)
FROM employees
#②查询工资>①的员工号，姓名和工资。
SELECT last_name,employee_id,salary
FROM employees
WHERE salary>(

	SELECT AVG(salary)
	FROM employees
);


#3.查询各部门中工资比本部门平均工资高的员工的员工号, 姓名和工资
#①查询各部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id
#②连接①结果集和employees表，进行筛选
SELECT employee_id,last_name,salary,e.department_id
FROM employees e
INNER JOIN (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
ON e.department_id = ag_dep.department_id
WHERE salary>ag_dep.ag ;


#4.	查询和姓名中包含字母u的员工在相同部门的员工的员工号和姓名
#①查询姓名中包含字母u的员工的部门
SELECT  DISTINCT department_id
FROM employees
WHERE last_name LIKE '%u%'
#②查询部门号=①中的任意一个的员工号和姓名
SELECT last_name,employee_id
FROM employees
WHERE department_id IN(
	SELECT  DISTINCT department_id
	FROM employees
	WHERE last_name LIKE '%u%'
);


#5. 查询在部门的location_id为1700的部门工作的员工的员工号
#①查询location_id为1700的部门
SELECT DISTINCT department_id
FROM departments 
WHERE location_id  = 1700
#②查询部门号=①中的任意一个的员工号
SELECT employee_id
FROM employees
WHERE department_id =ANY(
	SELECT DISTINCT department_id
	FROM departments 
	WHERE location_id  = 1700
);

#6.查询管理者是King的员工姓名和工资
#①查询姓名为king的员工编号
SELECT employee_id
FROM employees
WHERE last_name  = 'K_ing'
#②查询哪个员工的manager_id = ①
SELECT last_name,salary
FROM employees
WHERE manager_id IN(
	SELECT employee_id
	FROM employees
	WHERE last_name  = 'K_ing'

);

#7.查询工资最高的员工的姓名，要求first_name和last_name显示为一列，列名为 姓.名
#①查询最高工资
SELECT MAX(salary)
FROM employees
#②查询工资=①的姓.名
SELECT CONCAT(first_name,last_name) "姓.名"
FROM employees
WHERE salary=(
	SELECT MAX(salary)
	FROM employees
);
```








