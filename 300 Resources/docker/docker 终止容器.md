---
Create: 2021年 十二月 26日, 星期日 19:14
tags: 
  - docker

---
# 终止

可以使用 `docker container stop` 来终止一个运行中的容器。

> 当 Docker 容器中指定的应用终结时，容器也自动终止。

对于只启动了一个终端的容器，用户通过 `exit` 命令或 `Ctrl+d` 来退出终端时，所创建的容器立刻终止。终止状态的容器可以用 `docker container ls -a` 命令看到。

```
docker container ls -a
CONTAINER ID        IMAGE                    COMMAND                CREATED             STATUS                          PORTS               NAMES
ba267838cc1b        ubuntu:18.04             "/bin/bash"            30 minutes ago      Exited (0) About a minute ago                       trusting_newton
```

处于终止状态的容器，可以通过 `docker container start` 命令来重新启动。此外，`docker container restart` 命令会将一个运行态的容器终止，然后再重新启动它。




