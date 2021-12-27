---
Create: 2021年 十二月 12日, 星期日 23:44
tags: 
  - Engineering/redis
  - 大数据
---

1.  keys *
2.  exists key的名字，判断某个key是否存在
3.  move key db   --->当前库就没有了，被移除了
4.  expire key 秒钟：为给定的key设置过期时间
5.  ttl key 查看还有多少秒过期，-1表示永不过期，-2表示已过期
6.  type key 查看你的key是什么类型


