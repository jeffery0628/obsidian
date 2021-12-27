---
Create: 2021年 十二月 26日, 星期日 19:45
tags: 
  - docker

---

# 主机安装显卡驱动
## 查看显卡硬件型号

```
ubuntu-drivers devices
```

输出如下内容：

```
== /sys/devices/pci0000:00/0000:00:01.0/0000:01:00.0 ==
modalias : pci:v000010DEd00001E87sv00001458sd000037A7bc03sc00i00
vendor   : NVIDIA Corporation
model    : TU104 [GeForce RTX 2080 Rev. A]
driver   : nvidia-driver-450-server - distro non-free
driver   : nvidia-driver-450 - distro non-free
driver   : nvidia-driver-460-server - distro non-free
driver   : nvidia-driver-418-server - distro non-free
driver   : nvidia-driver-460 - distro non-free recommended
driver   : xserver-xorg-video-nouveau - distro free builtin
```

## 安装显卡驱动

```
sudo ubuntu-drivers autoinstall  # 直接安装推荐驱动 (与下面一条命令，二者选其一执行即可)
sudo apt install nvidia-driver-460 # 安装指定的驱动
```


# 安装nvidia Container Toolkit

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit

sudo systemctl restart docker
```

# 抓取镜像

到 docker.com 上选择对应的深度学习环境镜像 抓取启动即可。

```
docker pull pytorch/pytorch:latest
docker pull nvidia/cuda:10.2-runtime-ubuntu18.04
```


# 启动容器

## 映射显卡

```
 docker run --gpus all 镜像ID 启动后的命令
 eg:
 docker run --gpus all pytorch/pytorch:latest nvidia-smi
 docker run --gpus device=0 pytorch/pytorch:latest nvidia-smi  # 指定GPU 1，运行容器
 docker run --gpus 2 pytorch/pytorch:latest nvidia-smi  # 启动支持双GPU的容器
```

## 挂载目录

```
docker run -dit -v /home/lizhen/workspace/shouhu_text_match:/data --gpus all pytorch/pytorch:latest  /bin/bash
```



# 将容器保存为镜像

```
docker commit [选项] [容器ID或容器名] [仓库名:标签]

docker commit -a 'pytorch36' dd7 pytorch:36
```



# 将镜像保存导出

```
docker save pytorch:36 > pytorch36.tar
docker save pytorch:36 -o pytorch36.tar
```

# 将镜像导入

```
docker load < pytorch36.tar
```






