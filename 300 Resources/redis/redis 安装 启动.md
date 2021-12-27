---
Create: 2021年 十二月 11日, 星期六 23:22
tags: 
  - Engineering/redis
  - 大数据
---

## 下载
[英文官网](http://redis.io/)
[中文官网](http://www.redis.cn/)

## ubuntu 20.04 安装

### 安装
将下载下来的安装包解压到/opt 目录下
```
 sudo tar -zxvf redis-5.0.14.tar.gz
```

进入 redis-5.0.14目录下，执行make
```
cd redis-5.0.14
sudo make
```

> 可能出现的问题
> 1. 安装gcc
> 2. jemalloc/jemalloc.h：没有那个文件或目录：运行sudo make distclean之后再 sudo make

如果make完成后继续执行
```
sudo make install
```


### 查看安装目录
进入默认安装目录：
```
cd /usr/local/bin
```
该目录下，文件介绍：
1. redis-server        redis服务器 
2. redis-cli            redis命令行客户端 
3. redis-benchmark        redis性能测试工具
4. redis-check-aof        aof文件修复工具 
5. redis-check-dump    rdb文件检查工具


### redis 启动命令
#### 默认启动
```
redis-server
```

#### 配置文件启动
拷贝/opt/redis-5.0.14/redis.conf 到 /myconf 目录下
修改/myconf 目录下配置文件 redis.conf ，将文件中daemonize no 改成 yes，让服务启动

```
redis-server /myconf/redis.conf
```
![[700 Attachments/720 pictures/Pasted image 20211212154145.png]]


#### 配置后台启动
```
# 配置后台启动，且端口是 1123  
redis-server ./redis.conf --daemonize yes --port 1123
```



### redis-cli
```
# redis 交互
redis-cli

# 单实例停止Redis命令
redis-cli shutdown

# 多实例停止redis命令
redis-cli -p 6379 shutdown
```





