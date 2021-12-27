---
Create: 2021年 十二月 14日, 星期二 13:43
tags: 
  - Engineering/redis
  - 大数据
---
## 简介
反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库

## 使用

1. 调整结构，6379带着80、81
2. 自定义的/myredis目录下新建sentinel.conf文件，名字绝不能错
3. 配置哨兵,填写内容
	1.  sentinel monitor 被监控数据库名字(自己起名字) 127.0.0.1 6379 1，最后一个数字1，表示主机挂掉后salve投票看让谁接替成为主机，得票数多少后成为主机
4. 启动哨兵： Redis-sentinel /myredis/sentinel.conf   ![[700 Attachments/720 pictures/Pasted image 20211214134547.png]]



> 一组sentinel能同时监控多个Master



