---
Create: 2021年 十二月 26日, 星期日 17:56
tags: 
  - docker
---


# 安装`Docker`

Docker 分为 `stable` `test` 和 `nightly` 三个更新频道。

## Linux

### 卸载旧版本

旧版本的 Docker 称为 `docker` 或者 `docker-engine`，使用以下命令卸载旧版本：

```bash
sudo apt-get remove docker docker-engine  docker.io # ubuntu,Debian

sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-selinux docker-engine-selinux docker-engine # Fedora,CentOS
```

### 使用 apt 安装

由于 `apt` 源使用 HTTPS 以确保软件下载过程中不被篡改。因此，首先需要添加使用 HTTPS 传输的软件包以及 CA 证书。

```bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```

为了确认所下载软件包的合法性，需要添加软件源的 `GPG` 密钥。

```bash
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

# 官方源
# curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

然后，向 `sources.list` 中添加 Docker 软件源:

> 在一些基于 Debian 的 Linux 发行版中 `$(lsb_release -cs)` 可能不会返回 Debian 的版本代号，例如 `Kail Linux`、 `BunsenLabs Linux`。在这些发行版中我们需要将下面命令中的 `$(lsb_release -cs)` 替换为 https://mirrors.aliyun.com/docker-ce/linux/debian/dists/ 中支持的 Debian 版本代号，例如 `buster`。

```bash
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"


# 官方源
# sudo add-apt-repository \
#    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
#    $(lsb_release -cs) \
#    stable"
```

更新 apt 软件包缓存，并安装 `docker-ce`：

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

### 使用脚本安装

在测试或开发环境中 Docker 官方为了简化安装流程，提供了一套便捷的安装脚本，Ubuntu 系统上可以使用这套脚本安装，另外可以通过 `--mirror` 选项使用国内源进行安装：

> 若想安装测试版的 Docker, 需要从 test.docker.com 获取脚本

```bash
# curl -fsSL test.docker.com -o get-docker.sh # 测试版docker
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
# sudo sh get-docker.sh --mirror AzureChinaCloud
```

执行这个命令后，脚本就会自动的将一切准备工作做好，并且把 Docker 的稳定(stable)版本安装在系统中。

### 启动 Docker

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

### 建立 docker 用户组

默认情况下，`docker` 命令会使用 `Unix socket` 与 Docker 引擎通讯。而只有 `root` 用户和 `docker` 组的用户才可以访问 Docker 引擎的 Unix socket。

出于安全考虑，一般 Linux 系统上不会直接使用 `root` 用户。因此，更好地做法是将需要使用 `docker` 的用户加入 `docker` 用户组。

建立 `docker` 组：

```bash
sudo groupadd docker
```

将当前用户加入 `docker` 组：

```bash
sudo usermod -aG docker $USER
```

### 测试 Docker 是否安装正确

```bash
docker run hello-world
```

若能正常输出以上信息，则说明安装成功。



## MacOS

### 使用 Homebrew 安装

```
brew cask install docker
```

### 手动下载安装

 [下载地址](https://desktop.docker.com/mac/stable/Docker.dmg)  Docker Desktop for Mac。

![[700 Attachments/Pasted image 20211226175826.png]]

### 查看版本

在终端通过命令检查安装后的 Docker 版本。

```bash
docker --version
Docker version 20.10.0, build 7287ab3
```

如果 `docker version`、`docker info` 都正常的话，可以尝试运行一个 [Nginx 服务器](https://hub.docker.com/_/nginx/)：

```bash
docker run -d -p 80:80 --name webserver nginx
```

服务运行后，可以访问 [http://localhost](http://localhost/)，如果看到了 "Welcome to nginx!"，就说明 Docker Desktop for Mac 安装成功了。要停止 Nginx 服务器并删除执行下面的命令：

```bash
docker stop webserver
docker rm webserver
```

## Windows 10

[Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/) 支持 64 位版本的 Windows 10 Pro，且必须开启 Hyper-V。

 [下载地址](https://desktop.docker.com/win/stable/Docker Desktop Installer.exe) 下载 Docker Desktop for Windows。

或者 使用`winget`安装

```bash
winget install Docker.DockerDesktop
```




