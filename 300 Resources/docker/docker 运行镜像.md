---
Create: 2021年 十二月 26日, 星期日 18:30
tags: 
  - docker

---
# 运行

有了镜像后，就能够以这个镜像为基础启动并运行一个容器。以上面的 `ubuntu:18.04` 为例，如果打算启动里面的 `bash` 并且进行交互式操作的话，可以执行下面的命令:

```bash
docker run -it --rm ubuntu:18.04 bash
```

`docker run` 就是运行容器的命令:

- `-it`：这是两个参数，一个是 `-i`：交互式操作，一个是 `-t` 终端。我们这里打算进入 `bash` 执行一些命令并查看返回结果，因此我们需要交互式终端。

- `--rm`：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 `docker rm`。这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 `--rm` 可以避免浪费空间。

- `ubuntu:18.04`：这是指用 `ubuntu:18.04` 镜像为基础来启动容器。

- `bash`：放在镜像名后的是 **命令**，这里希望有个交互式 Shell，因此用的是 `bash`。

```bash
root@e7009c6ce357:/# cat /etc/os-release
NAME="Ubuntu"
VERSION="18.04.1 LTS (Bionic Beaver)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 18.04.1 LTS"
VERSION_ID="18.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=bionic
UBUNTU_CODENAME=bionic
```



进入容器后，可以在 Shell 下操作，执行任何所需的命令。这里执行了 `cat /etc/os-release`，这是 Linux 常用的查看当前系统版本的命令，从返回的结果可以看到容器内是 `Ubuntu 18.04.1 LTS` 系统。

最后通过 `exit` 退出了这个容器。




