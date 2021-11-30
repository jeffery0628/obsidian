---
Create: 2021年 十一月 30日, 星期二 13:05
tags: 
  - Engineering/MySql
  - 大数据
---
# 修改

## 修改单表：

```mysql
update 表名 
set 字段=新值,字段=新值 
【where 条件】
```



```mysql
#1：修改beauty表中姓唐的女神的电话为13899888899
UPDATE beauty SET phone = '13899888899'
WHERE NAME LIKE '唐%';

#2：修改boys表中id好为2的名称为张飞，魅力值 10
UPDATE boys SET boyname='张飞',usercp=10
WHERE id=2;

#3. 将3号员工的last_name修改为“drelxer”
UPDATE my_employees SET last_name='drelxer' WHERE id = 3;

#4.将所有工资少于900的员工的工资修改为1000
UPDATE my_employees SET salary=1000 WHERE salary<900;
```



## 修改多表：

```mysql
update 表1 别名1,表2 别名2
set 字段=新值，字段=新值
where 连接条件
and 筛选条件
```

```mysql
#1：修改张无忌的女朋友的手机号为119
UPDATE boys bo
INNER JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`phone`='119',bo.`userCP`=1000
WHERE bo.`boyName`='张无忌';

#2：修改没有男朋友的女神的男朋友编号都为2号
UPDATE boys bo
RIGHT JOIN beauty b ON bo.`id`=b.`boyfriend_id`
SET b.`boyfriend_id`=2
WHERE bo.`id` IS NULL;

#3.将userid 为Bbiri的user表和my_employees表的记录全部删除
DELETE u,e
FROM users u
JOIN my_employees e ON u.`userid`=e.`Userid`
WHERE u.`userid`='Bbiri';
```



## sql99

```mysql
update 表1 别名
inner|left|right join 表2 别名
on 连接条件
set 列=值,...
where 筛选条件;
```







