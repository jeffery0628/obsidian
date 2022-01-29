---
Create: 2021年 十二月 27日, 星期一 22:13
tags: 
  - Engineering/MySql
  - 大数据

---

## 永久修改
修改配置文件：(在mysqld节点下最后加上中文字符集配置)
```bash
vim /etc/my.cnf

character_set_server=utf8
```
![[700 Attachments/Pasted image 20211227221451.png]]

            

重新启动mysql服务后再次查看：
![[700 Attachments/Pasted image 20211227221514.png]]
> 注意：已经创建的数据库的设定不会发生变化，参数修改只对新建的数据库有效！




## 变更生成的库表字符集
修改数据库的字符集
```bash
alter database 数据库名 character set 'utf8';
```

修改数据表的字符集
```bash
alter table 表名 convert to  character set 'utf8';
```


