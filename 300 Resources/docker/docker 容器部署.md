---
Create: 2021年 十二月 26日, 星期日 19:38
tags: 
  - docker

---
## MySQL部署

### 拉取mysql镜像

```
docker pull centos/mysql-57-centos7
```

### 创建容器

```
docker run -di --name=tensquare_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

> -p 代表端口映射，格式为  宿主机映射端口:容器运行端口
>
> -e 代表添加环境变量  MYSQL_ROOT_PASSWORD  是root用户的登陆密码

### 远程登录mysql

连接宿主机的IP  ,指定端口为33306 

 ## tomcat部署

### 拉取镜像

```
docker pull tomcat:7-jre7
```

### 创建容器

创建容器  -p表示地址映射

```
docker run -di --name=mytomcat -p 9000:8080 
-v /usr/local/webapps:/usr/local/tomcat/webapps tomcat:7-jre7
```

## Nginx部署 

### 拉取镜像	

```
docker pull nginx
```

### 创建Nginx容器

```
docker run -di --name=mynginx -p 80:80 nginx
```

## Redis部署

### 拉取镜像

```
docker pull redis
```

### 创建容器

```
docker run -di --name=myredis -p 6379:6379 redis
```






