---
Create: 2021年 十二月 26日, 星期日 17:59
tags: 
  - docker
---

## 镜像加速

国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。国内云服务商：

- [阿里云加速器(点击管理控制台 -> 登录账号(淘宝账号) -> 右侧镜像中心 -> 镜像加速器 -> 复制地址)](https://www.aliyun.com/product/acr?source=5176.11533457&userCode=8lx5zmtu)

- [网易云加速器 `https://hub-mirror.c.163.com`](https://www.163yun.com/help/documents/56918246390157312)

- [百度云加速器 `https://mirror.baidubce.com`](https://cloud.baidu.com/doc/CCE/s/Yjxppt74z#使用dockerhub加速器)

由于镜像服务可能出现宕机，建议同时配置多个镜像。

**镜像站测试** [**docker-practice/docker-registry-cn-mirror-test**](https://github.com/docker-practice/docker-registry-cn-mirror-test/actions) **查看。**

### Ubuntu 16.04+、Debian 8+、CentOS 7+

首先执行以下命令，查看是否在 `docker.service` 文件中配置过镜像地址。

```bash
systemctl cat docker | grep '\-\-registry\-mirror'
```

如果该命令有输出，那么执行 `$ systemctl cat docker` 查看 `ExecStart=` 出现的位置，修改对应的文件内容去掉 `--registry-mirror` 参数及其值，并按接下来的步骤进行配置。

如果以上命令没有任何输出，那么可以在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在,新建该文件）：

```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```

之后重新启动服务。

```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### windows 10

对于 `Windows 10` 用户，在任务栏托盘 Docker 图标内右键菜单选择 `Settings`，打开配置窗口后在左侧导航菜单选择 `Docker Engine`，在右侧像下边一样编辑 json 文件，之后点击 `Apply & Restart` 保存后 Docker 就会重启并应用配置的镜像地址了。

```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```

### MacOS

对于 macOS 用户，在任务栏点击 Docker Desktop 应用图标 -> `Perferences`，在左侧导航菜单选择 `Docker Engine`，在右侧像下边编辑 json 文件。修改完成之后，点击 `Apply & Restart` 按钮，Docker 就会重启并应用配置的镜像地址了。

```json
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```

### 检查加速器是否生效

执行 `$ docker info`，如果从结果中看到了如下内容，说明配置成功。

```bash
Registry Mirrors:
 https://hub-mirror.c.163.com/
```

### `k8s.gcr.io` 镜像

可以登录 [阿里云 容器镜像服务](https://www.aliyun.com/product/acr?source=5176.11533457&userCode=8lx5zmtu&type=copy) **镜像中心** -> **镜像搜索** 查找。

例如 `k8s.gcr.io/coredns:1.6.7` 镜像可以用 `registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.7` 代替。







