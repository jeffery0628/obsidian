---
Create: 2021年 十二月 31日, 星期五 20:48
tags: 
  - Engineering/MySql
  - 大数据
---

## 建表插数
```sql
CREATE TABLE IF NOT EXISTS `class` (
`id` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`card` INT(10) UNSIGNED NOT NULL,
PRIMARY KEY (`id`)
);
CREATE TABLE IF NOT EXISTS `book` (
`bookid` INT(10) UNSIGNED NOT NULL AUTO_INCREMENT,
`card` INT(10) UNSIGNED NOT NULL,
PRIMARY KEY (`bookid`)
);
 
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));
INSERT INTO class(card) VALUES(FLOOR(1 + (RAND() * 20)));

```


## left join
①分析性能
![[700 Attachments/Pasted image 20211231205050.png]]

②如何优化？在哪个表上建立索引？
在左表建索引：
```sql
ALTER TABLE `book` ADD INDEX  idx_card( `card`);
```
![[700 Attachments/Pasted image 20211231205200.png]]
在右表建索引：
```sql
alter table class add index idx_card(card);
```
![[700 Attachments/Pasted image 20211231205620.png]]

> 结论：①在优化关联查询时，只有在被驱动表上建立索引才有效！②left join时，左侧的为驱动表，右侧为被驱动表！

## inner join
![[700 Attachments/Pasted image 20211231205759.png]]
![[700 Attachments/Pasted image 20211231205819.png]]
> 两个查询字段调换顺序，发现结果也是一样的！

![[700 Attachments/Pasted image 20211231210242.png]]
> 结论：inner join 时，mysql会自己帮你把小结果集的表选为驱动表。

![[700 Attachments/Pasted image 20211231210337.png]]
> straight_join: 效果和inner join一样，但是会强制将左侧作为驱动表！

## 关联查询案例分析
![[700 Attachments/Pasted image 20211231210510.png]]
![[700 Attachments/Pasted image 20211231210530.png]]
> 上述两个案例，第一个查询效率较高，且有优化的余地。第二个案例中，子查询作为被驱动表，由于子查询是虚表，无法建立索引，因此不能优化。

>结论：子查询尽量不要放在被驱动表，有可能使用不到索引；left join时，尽量让实体表作为被驱动表。

![[700 Attachments/Pasted image 20211231210638.png]]
![[700 Attachments/Pasted image 20211231210655.png]]

> 结论：能够直接多表关联的尽量直接关联，不用子查询！






