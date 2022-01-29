---
Create: 2021年 十二月 31日, 星期五 21:35
tags: 
  - Engineering/MySql
  - 大数据
---

### 列出自己的掌门比自己年龄小的人员：
![[700 Attachments/Pasted image 20211231213651.png]]
更换为大表，进行分析：
![[700 Attachments/Pasted image 20211231213708.png]]

两次inner join的被驱动表都已经用上了索引。
### 列出所有年龄低于自己门派平均年龄的人员
思路： 先取门派的平均年龄，再跟自己的年龄做对比！
![[700 Attachments/Pasted image 20211231213803.png]]

更换为大表：
在没有索引的前提下：
![[700 Attachments/Pasted image 20211231213837.png]]
如何优化：
1. 首先在子查询中，需要根据deptid做groupby操作，因此，需要在deptid上面建立索引；
2. 因为是inner join,因此会自动将小表作为驱动表，也就是说，分组后的tmp是驱动表，而e1是被驱动表；
3. 而在e1中，需要查询deptid和age两个字段，因此这两个字段也需要建立索引
结果：创建deptid和age的符合索引:   create index idx_deptid_age on emp(deptid,age);
![[700 Attachments/Pasted image 20211231213912.png]]

### 列出至少有2个年龄大于40岁的成员的门派名称
思路： 先查询大于40岁的成员，然后按照门派分组，然后再判断至少有2个的门派！
![[700 Attachments/Pasted image 20211231213945.png]]

大表优化：
![[700 Attachments/Pasted image 20211231214007.png]]

优化：
1. 两表关联，我们可以考虑将小表作为驱动表。
2. group by的字段 id,deptName还可以建立索引:
	```sql
	create index idx_id_deptName on dept(id,deptName);
	```
3. 被驱动表的deptid作为关联字段，可以建立索引：
	```sql
	create index idx_deptid on emp(deptid);
	create index idx_id_deptname on dept(id,deptName);
	```
	
![[700 Attachments/Pasted image 20211231214146.png]]


### 至少有2位非掌门人成员的门派
![[700 Attachments/Pasted image 20211231214215.png]]

切换大表：
没有索引的情况下：
![[700 Attachments/Pasted image 20211231214245.png]]
优化分析： 三个表关联，然后做group by分组！
1. group by 的字段，可以加上索引：
	```sql
	create index idx_deptname_id on dept(deptName,id);
	```
2. 可以将部门表作为驱动表
3. 第一次join时，e表作为被驱动表，可以将deptid设置索引：
	```sql
	create index idx_deptid on emp(deptid);
	```
4. 最有一次join中，使用了dept表作为被驱动表，查询ceo字段，因此可以在ceo上面建立索引:
	```sql
	create index idx_ceo on dept(ceo);
	```
![[700 Attachments/Pasted image 20211231214426.png]]


### 列出全部人员，并增加一列备注“是否为掌门”，如果是掌门人显示是，不是掌门人显示否

![[700 Attachments/Pasted image 20211231214455.png]]
大表关联：
```sql
explain select e.name,case when d.id is null then '否' else '是' end '是否为掌门' from emp e 
 left join dept d 
 on e.id=d.ceo;
```
优化：在d表的ceo字段建立索引即可！

### 列出全部门派，并增加一列备注“老鸟or菜鸟”，若门派的平均值年龄>40显示“老鸟”，否则显示“菜鸟”
思路： 先从emp表求出，各门派的平均年龄，分组，然后在关联dept表，使用if函数进行判断！
select d.deptName,if(avg(age)>40,'老鸟','菜鸟') from t_emp e inner join t_dept d 
![[700 Attachments/Pasted image 20211231214605.png]]

切换大表：
![[700 Attachments/Pasted image 20211231214628.png]]

优化：
1. 使用dept作为驱动表
2. 在dept上建立deptName和id的索引：
	```sql
	create index idx_deptName_id on dept(deptName,id);
	```
3. 在emp上建立deptid字段的索引：
	```sql 
	create index index_deptid on emp(deptid);
	```
	
### 显示每个门派年龄最大的人
思路：先查询emp表，求出每个门派年龄最大的人，并按照deptid分组；然后再次关联emp表，关联其他的信息！
![[700 Attachments/Pasted image 20211231214743.png]]

优化前：
![[700 Attachments/Pasted image 20211231214806.png]]

优化思路：
1. 子查询中，emp表根据deptid进行分组，因此可以建立deptid字段的索引；
2. inner join查询中，关联了age和deptid，因此可以在deptid,age字段建立索引
	```sql
	create index idx_deptid_age on emp(deptid,age);
	```
	
![[700 Attachments/Pasted image 20211231214851.png]]







