---
Create: 2021年 十二月 31日, 星期五 20:48
tags: 
  - Engineering/MySql
  - 大数据
---

取所有不为掌门人的员工，按年龄分组！
![[700 Attachments/Pasted image 20211231210822.png]]

如何优化？
解决dept表的全表扫描，建立ceo字段的索引：
![[700 Attachments/Pasted image 20211231210851.png]]
此时，再次查询：
![[700 Attachments/Pasted image 20211231210926.png]]

进一步优化，替换not in。
![[700 Attachments/Pasted image 20211231211011.png]]

> 结论： 在范围判断时，尽量不要使用not in和not exists，使用 left join on xxx is null代替。








