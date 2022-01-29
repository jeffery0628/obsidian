---
Create: 2022年 一月 3日, 星期一 13:35
tags: 
  - linux

---
## centos 启用service
```
yum install initscripts -y
```

## 安装ssh服务
```bash
yum -y install openssh-server
yum -y install openssh-clients
```

## 配置
```bash
ssh-copy-id hadoop01
ssh-copy-id hadoop02
ssh-copy-id hadoop03
ssh-copy-id hadoop04
ssh-copy-id hadoop05
#输入yes,密码为Dockerfile建立时的密码:root
```






