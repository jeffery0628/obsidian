---
Create: 2021年 十二月 12日, 星期日 15:44
tags: 
  - Engineering/redis
  - 大数据
---
## 数据库
1. 默认16个数据库，类似数组下表从零开始，初始默认使用零号库，可以使用SELECT 命令在连接上指定数据库id  databases 16
2. 使用select 来切换数据库，比如 select 1，选择使用1号库
3. dbsize 查看当前数据库的key的数量
4. flushdb：清空当前库
5. Flushall：清空所有库中数据
6. 统一密码管理：16个库都是同样的密码
7. 默认端口是6379






