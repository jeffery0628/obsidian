---
Create: 2021年 十二月 27日, 星期一 22:32
tags: 
  - Engineering/MySql
  - 大数据
---


| 命令                                                         | 描述                                     | 备注                                                         |
| ------------------------------------------------------------ | ---------------------------------------- | ------------------------------------------------------------ |
| create user zhang3 identified by '123123';                   | 创建名称为zhang3的用户，密码设为123123； |                                                              |
| select host,user,password,select_priv,insert_priv,drop_priv  from mysql.user; | 查看用户和权限的相关信息                 |                                                              |
| set  password =password('123456')                            | 修改当前用户的密码                       |                                                              |
| update  mysql.user set password=password('123456') where user='li4'; | 修改其他用户的密码                       | 所有通过user表的修改，必须用flush privileges;命令才能生效    |
| update  mysql.user set user='li4' where user='wang5';        | 修改用户名                               | 所有通过user表的修改，必须用flush privileges;命令才能生效    |
| drop  user li4                                               | 删除用户                                 | 不要通过delete from user  u where user='li4' 进行删除，系统会有残留信息保留。 |



## 示例

```bash
select host,user,password,select_priv,insert_priv,drop_priv from mysql.user;
```
![[700 Attachments/Pasted image 20211228100023.png]]
host :表示连接类型
1. % 表示所有远程通过 TCP方式的连接
2.  IP 地址 如 (192.168.1.2,127.0.0.1) 通过制定ip地址进行的TCP方式的连接
3.  机器名   通过制定i网络中的机器名进行的TCP方式的连接
4.   ::1   IPv6的本地ip地址  等同于IPv4的 127.0.0.1
5.    localhost 本地方式通过命令行方式的连接 ，比如mysql -u xxx -p 123xxx 方式的连接。

user:表示用户名:同一用户通过不同方式连接的权限是不一样的。

password:密码，所有密码串通过 password(明文字符串) 生成的密文字符串。加密算法为MYSQLSHA1 ，不可逆 。mysql 5.7 的密码保存到 authentication_string 字段中不再使用password 字段。
 
select_priv , insert_priv等 为该用户所拥有的权限。




