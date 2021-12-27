---
Create: 2021年 十二月 26日, 星期日 18:01
tags: 
  - docker
---




## 开启实验特性

一些 docker 命令或功能仅当 **实验特性** 开启时才能使用，请按照以下方法进行设置。

### 开启 Docker CLI 的实验特性

编辑 `~/.docker/config.json` 文件，新增如下条目：

```json
{
  "experimental": "enabled"
}
```

### 开启 Dockerd 的实验特性

编辑 `/etc/docker/daemon.json`，新增如下条目:

```json
{
  "experimental": true
}
```

