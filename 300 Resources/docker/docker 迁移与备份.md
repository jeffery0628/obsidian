---
Create: 2021年 十二月 26日, 星期日 19:40
tags: 
  - docker

---
## 容器保存为镜像

通过以下命令将容器保存为镜像

```
docker commit mynginx mynginx_i
```

## 镜像备份

通过以下命令将镜像保存为tar 文件

```
docker  save -o mynginx.tar mynginx_i
```

##  镜像恢复与迁移

执行此命令进行恢复

```
docker load -i mynginx.tar
```

-i 输入的文件,执行后再次查看镜像，可以看到镜像已经恢复




