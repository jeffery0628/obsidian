---
Create: 2021年 十二月 27日, 星期一 10:08
tags: 
  - Engineering/MySql
  - 大数据
---

# 启动、停止、登录、退出命令

 net start 服务名（启动服务）  
 net stop 服务名（停止服务）  
 mysql 【-h主机名 -P端口号 】-u用户名 -p密码  (登陆)  
 exit  ctrl+c  (退出)
 
 
 # 实操
 查看是否是开机自启：(默认是开机自启的)
 ```bash
 systemctl is-enabled mysqld
```
![[700 Attachments/Pasted image 20211227133741.png]]
查看启动状态：
```bash
systemctl status mysqld
```
![[700 Attachments/Pasted image 20211227133825.png]]
启动之后查看进程：
```bash
ps -ef | grep mysql
```
![[700 Attachments/Pasted image 20211227133902.png]]


如果要取消开机自启动，则输入命令ntsysv
```bash
ntsysv
```
出现以下界面：(使用空格取消选中，然后按TAB确定！)
![[700 Attachments/Pasted image 20211227134406.png]]
