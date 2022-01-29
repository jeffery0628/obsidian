---
Create: 2021年 十二月 26日, 星期日 19:53
tags: 
  - Engineering/MySql
  - 大数据
---


## centos7
### 检查当前系统是否安装过MySql

```bash
rpm -qa|grep mysql/mariadb
```
Linux在安装的时候，自带了mysql的相关组件。先卸载系统自带的mysql：
```bash
rpm -e --nodeps  mysql-libs/mariadb-libs
```


### 安装
[官网下载地址](http://dev.mysql.com/downloads/mysql/)
执行rpm安装，必须按照下面的顺序安装：
1. rpm -ivh mysql-community-common-5.7.16-1.el7.x86_64.rpm
2. rpm -ivh mysql-community-libs-5.7.16-1.el7.x86_64.rpm
3. rpm -ivh mysql-community-client-5.7.16-1.el7.x86_64.rpm
4. rpm -ivh mysql-community-server-5.7.16-1.el7.x86_64.rpm

检查安装是否成功：
```bash
mysqladmin --version
```
或者使用命令:
```bash
rpm -qa | grep mysql
```

### mysql 服务初始化
为了保证数据库目录为与文件的所有者为 mysql 登录用户，如果你是以 root 身份运行 mysql 服务，需要执行下面的命令初始化
```bash
mysqld --initialize --user=mysql
```
>  --initialize 选项默认以“安全”模式来初始化，则会为 root 用户生成一个密码并将该密码标记为过期，登录后你需要设置一个新的密码

查看临时密码：
```bash
cat /var/log/mysqld.log
```

启动MySQL的服务：
```bash
systemctl start mysqld
```

更新密码:首次登陆通过 mysql -uroot -p进行登录，在Enter password：录入初始化密码
```bash
mysql -u root -p
```
![[700 Attachments/Pasted image 20211227133510.png]]
因为初始化密码默认是过期的，所以查看数据库会报错
![[700 Attachments/Pasted image 20211227133538.png]]
修改密码：
```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY '你的密码'; 
```
设置完密码就可以用新密码登录，正常使用数据库了

