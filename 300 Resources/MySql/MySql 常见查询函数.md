---
Create: 2021年 十一月 30日, 星期二 10:02
tags: 
  - Engineering/MySql
  - 大数据
---

# 常见查询函数

## 字符函数

```mysql
concat拼接
substr截取子串
upper转换成大写
lower转换成小写
trim去前后指定的空格和字符
ltrim去左边空格
rtrim去右边空格
replace替换
lpad左填充
rpad右填充
instr返回子串第一次出现的索引
length 获取字节个数
```

```mysql
# length 获取参数值的字节个数
SELECT LENGTH('john');
SELECT LENGTH('张三丰hahaha');

# concat 拼接字符串
SELECT CONCAT(last_name,'_',first_name) 姓名 FROM employees;
#示例：将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),LOWER(first_name))  姓名 FROM employees;

# upper、lower
# 将姓变大写，名变小写，然后拼接
SELECT CONCAT(UPPER(last_name),LOWER(first_name))  姓名 FROM employees;

# substr、substring 注意：索引从1开始
#截取从指定索引处后面所有字符
SELECT SUBSTR('李莫愁爱上了陆展元',7)  out_put;
#截取从指定索引处指定字符长度的字符
SELECT SUBSTR('李莫愁爱上了陆展元',1,3) out_put;

# instr 返回子串第一次出现的索引，如果找不到返回0
SELECT INSTR('杨不殷六侠悔爱上了殷六侠','殷八侠') AS out_put;

# trim
SELECT LENGTH(TRIM('    张翠山    ')) AS out_put;
SELECT TRIM('aa' FROM 'aaaaaaaaa张aaaaaaaaaaaa翠山aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa')  AS out_put;

# lpad 用指定的字符实现左填充指定长度
SELECT LPAD('殷素素',2,'*') AS out_put;

# rpad 用指定的字符实现右填充指定长度
SELECT RPAD('殷素素',12,'ab') AS out_put;

# replace 替换
SELECT REPLACE('周芷若周芷若周芷若周芷若张无忌爱上了周芷若','周芷若','赵敏') AS out_put;
```

## 数学函数

```mysql
round 四舍五入
rand 随机数
floor向下取整
ceil向上取整
mod取余
truncate截断
```
```mysql
#round 四舍五入
SELECT ROUND(-1.55);
SELECT ROUND(1.567,2);

# ceil 向上取整,返回>=该参数的最小整数
SELECT CEIL(-1.02);

#floor 向下取整，返回<=该参数的最大整数
SELECT FLOOR(-9.99);

#truncate 截断
SELECT TRUNCATE(1.69999,1);

# mod取余
/*
mod(a,b) ：  a-a/b*b
mod(-10,-3):-10- (-10)/(-3)*（-3）=-1
*/
SELECT MOD(10,-3);
SELECT 10%3;
```



## 日期函数

```mysql
now当前系统日期+时间
curdate当前系统日期
curtime当前系统时间
year
month
monthname
day
hour
minute
second
str_to_date 将字符转换成日期
date_format将日期转换成字符
```
```mysql
#now 返回当前系统日期+时间
SELECT NOW();

#curdate 返回当前系统日期，不包含时间
SELECT CURDATE();

#curtime 返回当前时间，不包含日期
SELECT CURTIME();

#可以获取指定的部分，年、月、日、小时、分钟、秒
SELECT YEAR(NOW()) 年;
SELECT YEAR('1998-1-1') 年;
SELECT  YEAR(hiredate) 年 FROM employees;
SELECT MONTH(NOW()) 月;
SELECT MONTHNAME(NOW()) 月;

#str_to_date 将字符通过指定的格式转换成日期
SELECT STR_TO_DATE('1998-3-2','%Y-%c-%d') AS out_put;

#查询入职日期为1992--4-3的员工信息
SELECT * FROM employees WHERE hiredate = '1992-4-3';
SELECT * FROM employees WHERE hiredate = STR_TO_DATE('4-3 1992','%c-%d %Y');

#date_format 将日期转换成字符
SELECT DATE_FORMAT(NOW(),'%y年%m月%d日') AS out_put;

#查询有奖金的员工名和入职日期(xx月/xx日 xx年)
SELECT last_name,DATE_FORMAT(hiredate,'%m月/%d日 %y年') 入职日期
FROM employees
WHERE commission_pct IS NOT NULL;
```



## 流程控制函数

```mysql
if 处理双分支
case语句 处理多分支
	情况1：处理等值判断
	情况2：处理条件判断
```
```mysql
# if函数： if else 的效果
SELECT IF(10<5,'大','小');
SELECT last_name,commission_pct,IF(commission_pct IS NULL,'没奖金，呵呵','有奖金，嘻嘻') 备注
FROM employees;

#案例：查询员工的工资的情况，如果工资>20000,显示A级别；如果工资>15000,显示B级别；如果工资>10000，显示C级别；否则，显示D级别

SELECT salary,
CASE 
WHEN salary>20000 THEN 'A'
WHEN salary>15000 THEN 'B'
WHEN salary>10000 THEN 'C'
ELSE 'D'
END AS 工资级别
FROM employees;

# 案例：查询员工的工资，要求部门号=30，显示的工资为1.1倍；部门号=40，显示的工资为1.2倍；部门号=50，显示的工资为1.3倍；其他部门，显示的工资为原工资
SELECT salary 原始工资,department_id,
CASE department_id
WHEN 30 THEN salary*1.1
WHEN 40 THEN salary*1.2
WHEN 50 THEN salary*1.3
ELSE salary
END AS 新工资
FROM employees;


```



## 分组函数

```mysql
sum 求和
max 最大值
min 最小值
avg 平均值
count 计数
```
> 1. 以上五个分组函数都忽略null值，除了count(*)*
> 2. sum和avg一般用于处理数值型；max、min、count可以处理任何数据类型。
> 3. 都可以搭配distinct使用，用于统计去重后的结果
> 4. count的参数可以支持：
> 	1. 字段
> 	2. *
> 	3. 常量值
> 	4. 一般放1
> 5. 是否忽略null
> 	1. AVG()函数忽略列值为NULL的行。
> 	2. MAX()函数忽略列值为NULL的行。
> 	3. MIN()函数忽略列值为NULL的行。
> 	4. SUM()函数忽略列值为NULL的行。
> 	5. COUNT()函数有两种使用方式：

```mysql
SELECT SUM(salary) FROM employees;
SELECT AVG(salary) FROM employees;
SELECT MIN(salary) FROM employees;
SELECT MAX(salary) FROM employees;
SELECT COUNT(salary) FROM employees;

SELECT SUM(salary) 和,AVG(salary) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;

SELECT SUM(salary) 和,ROUND(AVG(salary),2) 平均,MAX(salary) 最高,MIN(salary) 最低,COUNT(salary) 个数
FROM employees;
```

### 和 distinct 搭配

```mysql
SELECT SUM(DISTINCT salary),SUM(salary) FROM employees;
SELECT COUNT(DISTINCT salary),COUNT(salary) FROM employees;
```

```mysql
#查询员工表中的最大入职时间和最小入职时间的相差天数 （DIFFRENCE）
SELECT MAX(hiredate) 最大,MIN(hiredate) 最小,(MAX(hiredate)-MIN(hiredate))/1000/3600/24 DIFFRENCE
FROM employees;

SELECT DATEDIFF(MAX(hiredate),MIN(hiredate)) DIFFRENCE
FROM employees;

SELECT DATEDIFF('1995-2-7','1995-2-6');
```









