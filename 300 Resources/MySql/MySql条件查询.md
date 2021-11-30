---
Create: 2021年 十一月 29日, 星期一 23:28
tags: 
  - Engineering/MySql
  - 大数据
---
# 条件查询

根据条件过滤原始表的数据，查询到想要的数据

语法：


```mysql
select 
	要查询的字段|表达式|常量值|函数
from 
	表名
where 
	条件 ;
```

条件运算符：`>`、`<`、 `>=`、 `<=`、 `=`、`!=` 、`<>`

逻辑运算符：
- `and`:两个条件如果同时成立，结果为true，否则为false
- `or`：两个条件只要有一个成立，结果为true，否则为false
- `not`：如果条件成立，则not后为false，否则为true

模糊查询：
 - like
- between and
- in
- is null


## 按条件表达式筛选

条件运算符：`>`、`<`、 `>=`、 `<=`、 `=`、`!=` 、`<>`

```mysql
#案例1：查询工资>12000的员工信息
SELECT 
	*
FROM
	employees
WHERE
	salary>12000;
	
#案例2：查询部门编号不等于90号的员工名和部门编号
SELECT 
	last_name,
	department_id
FROM
	employees
WHERE
	department_id<>90;	

```

## 按逻辑表达式筛选

逻辑运算符：

- `and`:两个条件如果同时成立，结果为true，否则为false
- `or`：两个条件只要有一个成立，结果为true，否则为false
- `not`：如果条件成立，则not后为false，否则为true

```mysql
#案例1：查询工资在10000到20000之间的员工名、工资以及奖金
SELECT
	last_name,
	salary,
	commission_pct
FROM
	employees
WHERE
	salary>=10000 AND salary<=20000;
	
#案例2：查询部门编号不是在90到110之间，或者工资高于15000的员工信息
SELECT
	*
FROM
	employees
WHERE
	NOT(department_id>=90 AND  department_id<=110) OR salary>15000;
```


## 模糊查询

模糊查询：

- like
- between and
- in
- is null  | is not null

### like

特点：一般和通配符搭配使用

通配符：

- % 任意多个字符,包含0个字符
- _ 任意单个字符

```mysql
#案例1：查询员工名中包含字符a的员工信息
select 
	*
from
	employees
where
	last_name like '%a%';#abc
	
#案例2：查询员工名中第三个字符为e，第五个字符为a的员工名和工资
select
	last_name,
	salary
FROM
	employees
WHERE
	last_name LIKE '__n_l%';
	
#案例3：查询员工名中第二个字符为_的员工名
SELECT
	last_name
FROM
	employees
WHERE
	last_name LIKE '_\_%'; # last_name LIKE '_$_%' ESCAPE '$';
```

### between and

1. 使用between and 可以提高语句的简洁度
2. 包含临界值
3. 两个临界值不要调换顺序

```mysql
#案例1：查询员工编号在100到120之间的员工信息

SELECT
	*
FROM
	employees
WHERE
	employee_id >= 120 AND employee_id<=100;
#----------------------
SELECT
	*
FROM
	employees
WHERE
	employee_id BETWEEN 120 AND 100;
```

### in 

含义：判断某字段的值是否属于in列表中的某一项

特点：

1. 使用in提高语句简洁度
2. in列表的值类型必须一致或兼容
3. in列表中不支持通配符

```mysql
#案例：查询员工的工种编号是 IT_PROG、AD_VP、AD_PRES中的一个员工名和工种编号
SELECT
	last_name,
	job_id
FROM
	employees
WHERE
	job_id = 'IT_PROT' OR job_id = 'AD_VP' OR JOB_ID ='AD_PRES';
#------------------
SELECT
	last_name,
	job_id
FROM
	employees
WHERE
	job_id IN( 'IT_PROT' ,'AD_VP','AD_PRES');
```

### is

=或<>不能用于判断null值

is null或is not null 可以判断null值

```mysql
#案例1：查询没有奖金的员工名和奖金率
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct IS NULL;


#案例1：查询有奖金的员工名和奖金率
SELECT
	last_name,
	commission_pct
FROM
	employees
WHERE
	commission_pct IS NOT NULL;
	
#案例2： 查询工资为12000的员工的姓和奖金率
SELECT
	last_name,
	commission_pct
FROM
	employees

WHERE 
	salary IS 12000;
```




