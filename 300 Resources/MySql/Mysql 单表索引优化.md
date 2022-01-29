---
Create: 2021年 十二月 31日, 星期五 09:20
tags: 
  - Engineering/MySql
  - 大数据
---

[TOC]

## 全值匹配
### 建立索引
```sql
CREATE INDEX idx_age_deptid_name ON emp(age,deptid,NAME);
```
![[700 Attachments/Pasted image 20211231100940.png]]
> 结论：全值匹配指的是：查询的字段按照顺序在索引中都可以匹配到！

![[700 Attachments/Pasted image 20211231101120.png]]
SQL中查询字段的顺序，跟使用索引中字段的顺序，没有关系。优化器会在不影响SQL执行结果的前提下，给你自动地优化。

## 最佳左前缀法则
查询字段与索引字段顺序的不同会导致，索引无法充分使用，甚至索引失效！
==原因==：使用复合索引，需要遵循最佳左前缀法则，即如果索引了多列，要遵守最左前缀法则。指的是查询从索引的最左前列开始并且不跳过索引中的列。

==结论==：过滤条件要使用索引必须按照索引建立时的顺序，依次满足，一旦跳过某个字段，索引后面的字段都无法被使用。
![[700 Attachments/Pasted image 20211231101420.png]]



## 不要再索引列上做任何操作
不在索引列上做任何操作（计算、函数、(自动or手动)类型转换），会导致索引失效而转向全表扫描。
### 在查询列上使用函数
![[700 Attachments/Pasted image 20211231132322.png]]
> 结论：等号左边无计算！
### 在查询列上做了转换
```sql
create index idx_name on emp(name);
```
![[700 Attachments/Pasted image 20211231133154.png]]
> 结论：等号右边无转换！

## 索引列上使用范围查询
![[700 Attachments/Pasted image 20211231133236.png]]
> 建议：将可能做范围查询的字段的索引顺序放在最后

## 使用不等于索引失效
mysql 在使用不等于(!= 或者<>)时，有时会无法使用索引会导致全表扫描。
![[700 Attachments/Pasted image 20211231133612.png]]

## 索引上的null
当字段允许为Null的条件下：
![[700 Attachments/Pasted image 20211231133647.png]]
>  is not null用不到索引，is null可以用到索引。

## 模糊查询索引失效
> 前缀不能出现模糊匹配！

## 字符串导致的索引失效
![[700 Attachments/Pasted image 20211231134416.png]]

## 减少使用or
![[700 Attachments/Pasted image 20211231134431.png]]
> 使用union all或者union来替代：

![[700 Attachments/Pasted image 20211231134513.png]]

## 覆盖索引
即查询列和索引列一致，不要写select 
![[700 Attachments/Pasted image 20211231134559.png]]

>覆盖索引是select的数据列只用从索引中就能够取得，不必读取数据行，换句话说查询列要被所建的索引覆盖

## 练习

| **Where****语句**                                       | **索引是否被使用**                                           |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| where a = 3                                             | Y,使用到a                                                    |
| where a = 3 and b = 5                                   | Y,使用到a，b                                                 |
| where a = 3 and b = 5  and c = 4                        | Y,使用到a,b,c                                                |
| where b = 3 或者 where b = 3 and c = 4 或者 where c = 4 | N                                                            |
| where a = 3 and c = 5                                   | 使用到a， 但是c不可以，b中间断了                             |
| where a = 3 and b  > 4 and c = 5                        | 使用到a和b， c不能用在范围之后，b断了                        |
| where a is null and b  is not null                      | is null 支持索引 但是is not null 不支持,所以 a 可以使用索引,但是 b不可以使用 |
| where a <>  3                                           | 不能使用索引                                                 |
| where  abs(a) =3                                        | 不能使用 索引                                                |
| where a = 3 and b  like 'kk%' and c = 4                 | Y,使用到a,b,c                                                |
| where a = 3 and b  like '%kk' and c = 4                 | Y,只用到a                                                    |
| where a = 3 and b  like '%kk%' and c = 4                | Y,只用到a                                                    |
| where a = 3 and b  like 'k%kk%' and c = 4               | Y,使用到a,b,c                                                |



