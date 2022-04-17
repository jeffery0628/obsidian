---
Create: 2022年 四月 14日, 星期四 21:54
tags: 
  - Engineering/hive
  - 大数据
---

# 等值Join
Hive支持通常的SQL JOIN语句，但是只支持等值连接，不支持非等值连接。
## 案例
根据员工表和部门表中的部门编号相等，查询员工编号、员工名称和部门名称；
```
select e.empno, e.ename, d.deptno, d.dname from emp e join dept d on e.deptno = d.deptno;
```

# 表的别名
优点：
1. 使用别名可以简化查询。
2. 使用表名前缀可以提高执行效率。

## 案例
合并员工表和部门表：
```
select e.empno, e.ename, d.deptno from emp e join dept d on e.deptno
 = d.deptno;
```

# 内连接
内连接：只有进行连接的两个表中都存在与连接条件相匹配的数据才会被保留下来。
```
select e.empno, e.ename, d.deptno 
from 
	emp e 
join 
	dept d 
on e.deptno = d.deptno;
```

# 左外连接
左外连接：JOIN操作符左边表中符合WHERE子句的所有记录将会被返回。
```
select e.empno, e.ename, d.deptno 
from 
	emp e 
left join 
	dept d 
on e.deptno = d.deptno;
```

# 右外连接
右外连接：JOIN操作符右边表中符合WHERE子句的所有记录将会被返回。
```
select e.empno, e.ename, d.deptno 
from 
	emp e 
right join 
	dept d 
on e.deptno = d.deptno;
```
# 满外连接
满外连接：将会返回所有表中符合WHERE语句条件的所有记录。如果任一表的指定字段没有符合条件的值的话，那么就使用NULL值替代。
```
select e.empno, e.ename, d.deptno 
from 
	emp e 
full join 
	dept d 
on e.deptno = d.deptno;
```

# 多表连接
连接 n个表，至少需要n-1个连接条件。例如：连接三个表，至少需要两个连接条件。
```
SELECT e.ename, d.dname, l.loc_name
FROM   
	emp e 
JOIN   
	dept d
ON d.deptno = e.deptno 

JOIN   
	location l
ON d.loc = l.loc;
```

大多数情况下，Hive会对每对JOIN连接对象启动一个MapReduce任务。
本例中会首先启动一个MapReduce job对表e和表d进行连接操作，然后会再启动一个MapReduce job将第一个MapReduce job的输出和表l;进行连接操作。

> 注意：为什么不是表d和表l先进行连接操作呢？
> 这是因为Hive总是按照从左到右的顺序执行的。

优化：当对3个或者更多表进行join连接时，如果每个on子句都使用相同的连接键的话，那么只会产生一个MapReduce job。

# 笛卡尔积
笛卡尔积会在下面条件下产生：
1. 省略连接条件
2. 连接条件无效
3. 所有表中的所有行互相连接

案例：
```
select empno, dname from emp, dept;
```