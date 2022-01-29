---
Create: 2021年 十二月 31日, 星期五 21:13
tags: 
  - Engineering/MySql
  - 大数据
---

where 条件和 on的判断这些过滤条件，作为优先优化的部分，是要被先考虑的！其次，如果有分组和排序，那么也要考虑grouo by 和order by。

## 无过滤，必排序
```sql
create index idx_age_deptid_name on emp (age,deptid,name);
```
![[700 Attachments/Pasted image 20211231211445.png]]
![[700 Attachments/Pasted image 20211231211457.png]]
using filesort说明进行了手工排序！原因在于没有where作为过滤条件！
![[700 Attachments/Pasted image 20211231211521.png]]
> 结论： 无过滤，不索引。where，limt都相当于一种过滤条件，所以才能使用上索引！

## 顺序错，必排序
![[700 Attachments/Pasted image 20211231211623.png]]
![[700 Attachments/Pasted image 20211231211645.png]]
> where 两侧列的顺序可以变换，效果相同，但是order by列的顺序不能随便变换！

## 方向反，必排序
![[700 Attachments/Pasted image 20211231211721.png]]
如果可以用上索引的字段都使用正序或者逆序，实际上是没有任何影响的，无非将结果集调换顺序。
![[700 Attachments/Pasted image 20211231211746.png]]
> 如果排序的字段，顺序有差异，就需要将差异的部分，进行一次倒置顺序，因此还是需要手动排序的！


## 索引的选择
首先，清除emp上面的所有索引，只保留主键索引:
```sql
drop index idx_age_deptid_name on emp;
```
年龄为30岁的，且员工编号小于101000的用户，按用户名称排序:
![[700 Attachments/Pasted image 20211231211911.png]]
表扫描肯定是不被允许的，因此需要考虑优化。思路：首先需要让where的过滤条件，用上索引；
查询中，age.empno是查询的过滤条件，而name则是排序的字段，因此我们来创建一个此三个字段的复合索引：
```sql
create index idx_age_empno_name on emp(age,empno,name);
```
![[700 Attachments/Pasted image 20211231212218.png]]
再次查询，发现using filesort依然存在。
原因： empno是范围查询，因此导致了索引失效，所以name字段无法使用索引排序。
所以，三个字段的符合索引，没有意义，因为empno和name字段只能选择其一！
解决： 鱼与熊掌不可兼得，因此，要么选择empno,要么选择name
```sql
drop index idx_age_empno_name on emp;
create index idx_age_name on emp(age,name);
create index idx_age_empno on emp(age,empno);

```
![[700 Attachments/Pasted image 20211231212307.png]]
![[700 Attachments/Pasted image 20211231212320.png]]

原因：所有的排序都是在条件过滤之后才执行的，所以如果条件过滤了大部分数据的话，几百几千条数据进行排序其实并不是很消耗性能，即使索引优化了排序但实际提升性能很有限。  相对的 empno<101000 这个条件如果没有用到索引的话，要对几万条的数据进行扫描，这是非常消耗性能的，使用empno字段的范围查询，过滤性更好（empno从100000开始）！

结论： 当范围条件和group by 或者 order by  的字段出现二选一时 ，优先观察条件字段的过滤数量，如果过滤的数据足够多，而需要排序的数据并不多时，优先把索引放在范围字段上。反之，亦然。

## group by
group by 使用索引的原则几乎跟order by一致 ，唯一区别是groupby 即使没有过滤条件用到索引，也可以直接使用覆盖索引。

![[700 Attachments/Pasted image 20211231212403.png]]









