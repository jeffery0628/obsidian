---
Create: 2021年 十二月 27日, 星期一 23:00
tags: 
  - Engineering/MySql
  - 大数据
---

## 单值索引
即一个索引只包含单个列，一个表可以有多个单列索引
### 随表一起创建
```sql
            

CREATE TABLE customer (id INT(10) UNSIGNED AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),

 PRIMARY KEY(id),

 KEY (customer_name)

);
```

### 单独建单值索引

```sql
CREATE  INDEX idx_customer_name ON customer(customer_name);
```



## 唯一索引
索引列的值必须唯一，但允许有空值

### 随表一起创建
```sql
REATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),
  PRIMARY KEY(id),
  KEY (customer_name),
  UNIQUE (customer_no)
);

```
### 单独建单值索引
```sql
CREATE UNIQUE INDEX idx_customer_no ON customer(customer_no);
```



## 主键索引

### 随表一起创建
```sql
CREATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),
  PRIMARY KEY(id) 
);

```
### 单独建单值索引
```sql
ALTER TABLE customer add PRIMARY KEY customer(customer_no);
```

### 删除主键索引
```sql
ALTER TABLE customer drop PRIMARY KEY ;
```
### 修改主键索引
必须先删除掉(drop)原索引，再新建(add)索引


## 复合索引
即一个索引包含多个列
### 随表一起创建
```sql
CREATE TABLE customer (id INT(10) UNSIGNED  AUTO_INCREMENT ,customer_no VARCHAR(200),customer_name VARCHAR(200),
  PRIMARY KEY(id),
  KEY (customer_name),
  UNIQUE (customer_name),
  KEY (customer_no,customer_name)
);

```

### 单独建单值索引
```sql
CREATE  INDEX idx_no_name ON customer(customer_no,customer_name);
```

## 基本语法

### 创建
```sql
CREATE  [UNIQUE ]  INDEX [indexName] ON table_name(column)) 
```

### 删除
```sql
DROP INDEX [indexName] ON mytable;
```

### 查看
```sql
SHOW INDEX FROM table_name\G
```

### 使用alter命令
添加一个主键，这意味着索引值必须是唯一的，且不能为NULL。
```sql
ALTER TABLE tbl_name ADD PRIMARY KEY (column_list) 
```

添加普通索引，索引值可出现多次。
```sql
ALTER TABLE tbl_name ADD INDEX index_name (column_list)
```

全文索引：指定了索引为 FULLTEXT
```sql
ALTER TABLE tbl_name ADD FULLTEXT index_name (column_list)
```

