---
Create: 2021年 十一月 30日, 星期二 13:08
tags: 
  - Engineering/MySql
  - 大数据
---

# 库和表的管理

创建： create

修改： alter

删除： drop

## 库

### 库的创建

语法：

```mysql
create database  [if not exists]库名;
```

```mysql
#案例：创建库Books
CREATE DATABASE IF NOT EXISTS books ;
```

### 库的修改

```mysql
# 修改库名
RENAME DATABASE books TO 新库名;
#更改库的字符集
ALTER DATABASE books CHARACTER SET gbk;
```

### 库的删除

```
库的删除
DROP DATABASE IF EXISTS books;
```

## 表

### 创建表

```mysql
# 创建表Book
CREATE TABLE book(
	id INT,#编号
	bName VARCHAR(20),#图书名
	price DOUBLE,#价格
	authorId  INT,#作者编号
	publishDate DATETIME#出版日期
);

# 创建表author
CREATE TABLE IF NOT EXISTS author(
	id INT,
	au_name VARCHAR(20),
	nation VARCHAR(10)

)

CREATE TABLE emp5(
id INT(7),
first_name VARCHAR(25),
last_name VARCHAR(25),
dept_id INT(7)

);
```

### 查看表结构

```
DESC book;
DESC author;
```

### 表的修改

语法：

```
alter table 表名 add|drop|modify|change column 列名 【列类型 约束】;
```

```mysql
#①修改列名
ALTER TABLE book CHANGE COLUMN publishdate pubDate DATETIME;

#②修改列的类型或约束
ALTER TABLE book MODIFY COLUMN pubdate TIMESTAMP;

#③添加新列
ALTER TABLE author ADD COLUMN annual DOUBLE; 

#④删除列
ALTER TABLE book_author DROP COLUMN  annual;

#⑤修改表名
ALTER TABLE author RENAME TO book_author;

# 将列Last_name的长度增加到50
ALTER TABLE emp5 MODIFY COLUMN last_name VARCHAR(50);

# 将表employees2重命名为emp5
ALTER TABLE employees2 RENAME TO emp5;

#在表dept和emp5中添加新列test_column，并检查所作的操作
ALTER TABLE emp5 ADD COLUMN test_column INT;

#直接删除表emp5中的列 dept_id
ALTER TABLE emp5 DROP COLUMN test_column;
```

### 表的删除

```mysql
#3.表的删除
DROP TABLE IF EXISTS book_author;

# 删除表emp5
DROP TABLE IF EXISTS emp5;
```





### 表的复制

#### 仅复制表结构

```mysql
CREATE TABLE copy LIKE author;
```

#### 复制表结构+数据

```mysql
CREATE TABLE copy2 
SELECT * FROM author;

# 根据表employees创建employees2
CREATE TABLE employees2 LIKE myemployees.employees;

#2.	将表departments中的数据插入新表dept2中
CREATE TABLE dept2
SELECT department_id,department_name
FROM myemployees.departments;
```

#### 复制表部分数据

```mysql
CREATE TABLE copy3
SELECT id,au_name
FROM author 
WHERE nation='中国';
```






