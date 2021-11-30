---
Create: 2021年 十一月 29日, 星期一 22:46
tags: 
  - Engineering/MySql
  - 大数据
---

# 启动、停止、登录、退出

 net start 服务名（启动服务）  
 net stop 服务名（停止服务）  
 mysql 【-h主机名 -P端口号 】-u用户名 -p密码  (登陆)  
 exit  ctrl+c  (退出)

# 语法规范

1.  不区分大小写,但建议关键字大写，表名、列名小写
2.  每条命令最好用分号结尾
3.  每条命令根据需要，可以进行缩进 或换行
4.  注释：
    -   单行注释：#注释文字
    -   单行注释：-- 注释文字
    -   多行注释：/* 注释文字 \*/

# SQL 语言分类
DQL（Data Query Language）：数据查询语言                -->   select 
DML(Data Manipulate Language):数据操作语言               -->  insert 、update、delete
DDL（Data Define Languge）：数据定义语言                  -->  create、drop、alter
TCL（Transaction Control Language）：事务控制语言  --> commit、rollback




