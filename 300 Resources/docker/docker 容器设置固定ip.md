---
Create: 2021年 十二月 26日, 星期日 22:53
tags: 
  - docker

---
## 三种网络类型
Docker安装后，默认会创建下面三种网络类型

```bash
docker network ls

NETWORK ID     NAME        DRIVER    SCOPE
f19be131cef0   bridge      bridge    local
352c99350159   host        host      local
95e2eba8f0fb   none        null      local
```
1. bridge:默认情况下启动的Docker容器，都是使用 bridge，Docker安装时创建的桥接网络，每次Docker容器重启时，会按照顺序获取对应的IP地址，这个就导致重启下，Docker的IP地址就变了
2. none:使用 --network=none ，docker 容器就不会分配局域网的IP
3. host:使用 --network=host，此时，Docker 容器的网络会附属在主机上，两者是互通的。例如，在容器中运行一个Web服务，监听8080端口，则主机的8080端口就会自动映射到容器中。


## 创建自定义网络类型
```bash
docker network create --subnet=192.168.1.0/24 hadoopnet
```

## 创建容器时指定ip
```bash
docker run -it --name hadoop_test --net hadoopnet --ip 192.168.1.31 centos:centos7 /bin/bash
```








