---
Create: 2021年 十二月 28日, 星期二 13:31
tags: 
  - Engineering/MySql
  - 大数据
---

## 常见的Join查询图
![[700 Attachments/Pasted image 20211228133357.png]]


## 示例
### 建表
```sql
CREATE TABLE `t_dept` (
 `id` INT(11) NOT NULL AUTO_INCREMENT,
 `deptName` VARCHAR(30) DEFAULT NULL,
 `address` VARCHAR(40) DEFAULT NULL,
 PRIMARY KEY (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;
CREATE TABLE `t_emp` (
 `id` INT(11) NOT NULL AUTO_INCREMENT,
 `name` VARCHAR(20) DEFAULT NULL,
  `age` INT(3) DEFAULT NULL,
 `deptId` INT(11) DEFAULT NULL,
empno int  not null,
 PRIMARY KEY (`id`),
 KEY `idx_dept_id` (`deptId`)
 #CONSTRAINT `fk_dept_id` FOREIGN KEY (`deptId`) REFERENCES `t_dept` (`id`)
) ENGINE=INNODB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8;

INSERT INTO t_dept(deptName,address) VALUES('华山','华山');
INSERT INTO t_dept(deptName,address) VALUES('丐帮','洛阳');
INSERT INTO t_dept(deptName,address) VALUES('峨眉','峨眉山');
INSERT INTO t_dept(deptName,address) VALUES('武当','武当山');
INSERT INTO t_dept(deptName,address) VALUES('明教','光明顶');
 INSERT INTO t_dept(deptName,address) VALUES('少林','少林寺');
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('风清扬',90,1,100001);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('岳不群',50,1,100002);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('令狐冲',24,1,100003);
 INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('洪七公',70,2,100004);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('乔峰',35,2,100005);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('灭绝师太',70,3,100006);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('周芷若',20,3,100007);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('张三丰',100,4,100008);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('张无忌',25,5,100009);
INSERT INTO t_emp(NAME,age,deptId,empno) VALUES('韦小宝',18,null,100010);
```

### 案例
所有有门派人员的信息：
```sql
SELECT e.`name`,d.`deptName` FROM t_emp e INNER JOIN t_dept d ON e.`deptId`=d.`id`;
```

列出所有人员及其门派信息
```sql
SELECT e.`name`,d.`deptName` FROM t_emp e LEFT JOIN t_dept d ON e.`deptId`=d.`id`;
```

列出所有门派及门派中的员工
```sql
SELECT *FROM t_dept d  LEFT JOIN t_emp e ON d.`id`=e.`dept_id`;
```

所有无门派人士
```sql
SELECT * FROM t_emp WHERE deptId IS NULL;
```

所有无人入的门派
```sql
SELECT d.* FROM  t_dept d LEFT JOIN t_emp e ON d.`id`=e.`deptId` WHERE e.`deptId` IS NULL;
```

所有人员和所有门派的对应关系
```sql
SELECT * FROM t_emp e LEFT JOIN t_dept d ON e.`deptId`=d.`id`
UNION ALL
SELECT * FROM t_emp e RIGHT JOIN t_dept d ON e.`deptId`=d.`id`;
```

所有没有入门派的人员和没人入的门派
```sql
SELECT * FROM t_emp e  LEFT JOIN t_dept d ON e.`deptId`=d.`id` WHERE e.deptId IS NULL
UNION ALL
SELECT * FROM  t_dept d LEFT JOIN t_emp e ON d.`id`=e.`deptId` WHERE e.`deptId` IS NULL;
```

添加CEO字段
```sql
ALTER TABLE `t_dept` 
add  CEO  INT(11)  ;
update t_dept set CEO=2 where id=1;
update t_dept set CEO=4 where id=2;
update t_dept set CEO=6 where id=3;
update t_dept set CEO=8 where id=4;
update t_dept set CEO=9 where id=5;

```

求各个门派对应的掌门人名称
```sql
SELECT d.deptName,e.name FROM t_dept d LEFT JOIN t_emp e ON d.ceo=e.id
```

求所有当上掌门人的平均年龄
```sql
SELECT AVG(e.age) FROM t_dept d LEFT JOIN t_emp e ON d.ceo=e.id
```

求所有人物对应的掌门名称
```sql
SELECT a.name,c.name ceoname FROM t_emp a
INNER JOIN t_dept b ON a.deptId = b.id
INNER JOIN t_emp c ON b.CEO = c.id;


SELECT a.name,(SELECT c.name FROM t_emp c WHERE c.id=b.CEO) ceoname 
FROM t_emp a
INNER JOIN t_dept b ON a.`deptId` = b.`id`;


SELECT ab.name,c.name ceoname FROM
(SELECT a.name,b.CEO FROM t_emp a
INNER JOIN t_dept b ON a.`deptId` = b.`id`) ab
INNER JOIN t_emp c ON ab.ceo = c.id;


SELECT c.name ,ab.ceoname FROM t_emp c 
INNER JOIN
(SELECT a.name ceoname,a.deptId FROM t_emp a 
INNER JOIN t_dept b ON b.CEO = a.id) ab
ON c.deptId = ab.deptId;

```

