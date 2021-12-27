---
Create: 2021年 十二月 26日, 星期日 19:01
tags: 
  - docker

---

## `Docker Hub`

目前 Docker 官方维护了一个公共仓库 [Docker Hub](https://hub.docker.com/)，其中已经包括了数量超过 [2,650,000](https://hub.docker.com/search/?type=image) 的镜像。大部分需求都可以通过在 Docker Hub 中直接下载镜像来实现。

### 注册

在 [https://hub.docker.com](https://hub.docker.com/) 免费注册一个 Docker 账号。

### 登录

通过执行 `docker login` 命令交互式的输入用户名及密码来完成在命令行界面登录 Docker Hub。

通过 `docker logout` 退出登录。

### 拉取镜像

通过 `docker search` 命令来查找官方仓库中的镜像，利用 `docker pull` 命令来将它下载到本地。

例如以 `centos` 为关键词进行搜索：

```
docker search centos
NAME                                            DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
centos                                          The official build of CentOS.                   465       [OK]
tianon/centos                                   CentOS 5 and 6, created using rinse instea...   28
blalor/centos                                   Bare-bones base CentOS 6.5 image                6                    [OK]
saltstack/centos-6-minimal                                                                      6                    [OK]
tutum/centos-6.4                                DEPRECATED. Use tutum/centos:6.4 instead. ...   5                    [OK]
```

> 可以看到返回了很多包含关键字的镜像，其中包括镜像名字、描述、收藏数（表示该镜像的受关注程度）、是否官方创建（OFFICIAL）、是否自动构建 （AUTOMATED）。

下载官方 `centos` 镜像到本地。

```
docker pull centos
Pulling repository centos
0b443ba03958: Download complete
539c0211cd76: Download complete
511136ea3c5a: Download complete
7064731afe90: Download complete
```

### 推送镜像

用户也可以在登录后通过 `docker push` 命令来将自己的镜像推送到 Docker Hub。

以下命令中的 `username` 请替换为 Docker 账号用户名。

```
docker tag ubuntu:18.04 username/ubuntu:18.04

docker image ls

REPOSITORY                                               TAG                    IMAGE ID            CREATED             SIZE
ubuntu                                                   18.04                  275d79972a86        6 days ago          94.6MB
username/ubuntu                                          18.04                  275d79972a86        6 days ago          94.6MB

docker push username/ubuntu:18.04

docker search username

NAME                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
username/ubuntu
```

### 自动构建

自动构建功能对于需要经常升级镜像内程序来说，十分方便。

有时候，用户构建了镜像，安装了某个软件，当软件发布新版本则需要手动更新镜像。

而自动构建允许用户通过 Docker Hub 指定跟踪一个目标网站（支持 [GitHub](https://github.com/) 或 [BitBucket](https://bitbucket.org/)）上的项目，一旦项目发生新的提交 （commit）或者创建了新的标签（tag），Docker Hub 会自动构建镜像并推送到 Docker Hub 中。

要配置自动构建，包括如下的步骤：

- 登录 Docker Hub；
- 在 Docker Hub 点击右上角头像，在账号设置（Account Settings）中关联（Linked Accounts）目标网站；
- 在 Docker Hub 中新建或选择已有的仓库，在 `Builds` 选项卡中选择 `Configure Automated Builds`；
- 选取一个目标网站中的项目（需要含 `Dockerfile`）和分支；
- 指定 `Dockerfile` 的位置，并保存。

之后，可以在 Docker Hub 的仓库页面的 `Timeline` 选项卡中查看每次构建的状态。







