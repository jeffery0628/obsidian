---
Create: 2021年 十一月 30日, 星期二 13:19
tags: 
  - Engineering/MySql
  - 大数据
---

# 视图

含义：理解成一张虚拟的表



| 视图和表的区别 | 使用方式 | 占用物理空间                |
| -------------- | -------- | --------------------------- |
| 视图           | 完全相同 | 不占用，仅仅保存的是sql逻辑 |
| 表             | 完全相同 | 占用                        |

视图的好处：

1. sql语句提高重用性，效率高
2. 和表实现了分离，提高了安全性

## 视图的创建

语法：

```mysql
CREATE VIEW  视图名
AS
查询语句;
```

```mysql
#案例：查询姓张的学生名和专业名
SELECT stuname,majorname
FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`
WHERE s.`stuname` LIKE '张%';

CREATE VIEW v1
AS
SELECT stuname,majorname
FROM stuinfo s
INNER JOIN major m ON s.`majorid`= m.`id`;

SELECT * FROM v1 WHERE stuname LIKE '张%';
```



## 视图的增删改查

### 创建视图

语法：

```mysql
create view 视图名
as
查询语句;
```

```mysql
#1.查询姓名中包含a字符的员工名、部门名和工种信息
#①创建
CREATE VIEW myv1
AS

SELECT last_name,department_name,job_title
FROM employees e
JOIN departments d ON e.department_id  = d.department_id
JOIN jobs j ON j.job_id  = e.job_id;
#②使用
SELECT * FROM myv1 WHERE last_name LIKE '%a%';


#2.查询各部门的平均工资级别

#①创建视图查看每个部门的平均工资
CREATE VIEW myv2
AS
SELECT AVG(salary) ag,department_id
FROM employees
GROUP BY department_id;
#②使用
SELECT myv2.`ag`,g.grade_level
FROM myv2
JOIN job_grades g
ON myv2.`ag` BETWEEN g.`lowest_sal` AND g.`highest_sal`;

#3.查询平均工资最低的部门信息
SELECT * FROM myv2 ORDER BY ag LIMIT 1;

#4.查询平均工资最低的部门名和工资
CREATE VIEW myv3
AS
SELECT * FROM myv2 ORDER BY ag LIMIT 1;

SELECT d.*,m.ag
FROM myv3 m
JOIN departments d
ON m.`department_id`=d.`department_id`;
```

### 查看视图

```mysql
SELECT * FROM my_v4;
SELECT * FROM my_v1 WHERE last_name='Partners';
```

### 插入视图

```mysql
INSERT INTO my_v4(last_name,department_id) VALUES('虚竹',90);
INSERT INTO myv1 VALUES('张飞','zf@qq.com');
```

### 修改更新视图

语法：

```mysql
# 方式一
create or replace view  视图名
as
查询语句;

# 方式二
alter view 视图名
as 
查询语句;
```

```mysql
SELECT * FROM myv3 

CREATE OR REPLACE VIEW myv3
AS
SELECT AVG(salary),job_id
FROM employees
GROUP BY job_id;

ALTER VIEW myv3
AS
SELECT * FROM employees;

UPDATE myv1 SET last_name = '张无忌' WHERE last_name='张飞';

```

### 删除视图

```mysql
DELETE FROM my_v4;
DELETE FROM myv1 WHERE last_name = '张无忌';
```

```mysql
DROP VIEW emp_v1,emp_v2,myv3;
```

###视图结构的查看	

```mysql
DESC test_v7;
SHOW CREATE VIEW test_v7;
```



### 不能更新的视图

> 某些视图不能更新:
>
> 包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all
> 常量视图:
> Select中包含子查询
> join
> from一个不能更新的视图
> where子句的子查询引用了from子句中的表

具备以下特点的视图不允许更新：

```mysql
#①包含以下关键字的sql语句：分组函数、distinct、group  by、having、union或者union all
CREATE OR REPLACE VIEW myv1
AS
SELECT MAX(salary) m,department_id
FROM employees
GROUP BY department_id;

SELECT * FROM myv1;
#更新
UPDATE myv1 SET m=9000 WHERE department_id=10;

#②常量视图
CREATE OR REPLACE VIEW myv2
AS
SELECT 'john' NAME;

SELECT * FROM myv2;
#更新
UPDATE myv2 SET NAME='lucy';





#③Select中包含子查询
CREATE OR REPLACE VIEW myv3
AS
SELECT department_id,(SELECT MAX(salary) FROM employees) 最高工资
FROM departments;

#更新
SELECT * FROM myv3;
UPDATE myv3 SET 最高工资=100000;


#④join
CREATE OR REPLACE VIEW myv4
AS
SELECT last_name,department_name
FROM employees e
JOIN departments d
ON e.department_id  = d.department_id;


SELECT * FROM myv4;
#更新
UPDATE myv4 SET last_name  = '张飞' WHERE last_name='Whalen';
INSERT INTO myv4 VALUES('陈真','xxxx');



#⑤from一个不能更新的视图
CREATE OR REPLACE VIEW myv5
AS
SELECT * FROM myv3;

SELECT * FROM myv5;
#更新
UPDATE myv5 SET 最高工资=10000 WHERE department_id=60;



#⑥where子句的子查询引用了from子句中的表
CREATE OR REPLACE VIEW myv6
AS
SELECT last_name,email,salary
FROM employees
WHERE employee_id IN(
	SELECT  manager_id
	FROM employees
	WHERE manager_id IS NOT NULL
);


SELECT * FROM myv6;
#更新
UPDATE myv6 SET salary=10000 WHERE last_name = 'k_ing';
```






