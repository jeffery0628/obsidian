---
Create: 2021年 十二月 27日, 星期一 22:39
tags: 
  - Engineering/MySql
  - 大数据
---

## 授予权限
| 命令                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| grant 权限1,权限2,…权限n  on 数据库名称.表名称 to 用户名@用户地址  identified by ‘连接口令’ | 该权限如果发现没有该用户，则会直接新建一个用户。  示例：  grant select,insert,delete,drop on atguigudb.* to  li4@localhost ;  给li4用户用本地命令行方式下，授予atguigudb这个库下的所有表的插删改查的权限。 |
| **grant  all privileges on \*.\* to root@'%'   identified by 'root';** | 授予通过网络方式登录的的joe用户 ，对所有库所有表的全部权限，密码设为123. |



## 收回权限
| 命令                                                         | 描述                                | 备注 |
| ------------------------------------------------------------ | ----------------------------------- | ---- |
| show grants                                                  | 查看当前用户权限                    |      |
| revoke [权限1,权限2,…权限n]  on   库名.表名   from 用户名@用户地址 ; | 收回权限命令                        |      |
| REVOKE ALL PRIVILEGES ON mysql.* FROM joe@localhost;         | 收回全库全表的所有权限              |      |
| REVOKE select,insert,update,delete ON mysql.* FROM joe@localhost; | 收回mysql库下的所有表的插删改查权限 |      |


> 权限收回后，必须用户重新登录后，才能生效。flush privileges;   所有通过user表的修改，必须用该命令才能生效。
