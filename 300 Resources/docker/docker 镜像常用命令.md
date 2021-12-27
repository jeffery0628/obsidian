---
Create: 2021年 十二月 26日, 星期日 19:28
tags: 
  - docker

---
# 镜像

## 查看镜像

```
docker images
```

> REPOSITORY：镜像名称
>
> TAG：镜像标签
>
> IMAGE ID：镜像ID
>
> CREATED：镜像的创建日期（不是获取该镜像的日期）
>
> SIZE：镜像大小

这些镜像都是存储在Docker宿主机的/var/lib/docker目录下

## 搜索镜像

如果需要从网络中查找需要的镜像，可以通过以下命令搜索

```
docker search 镜像名称
```

> NAME：仓库名称
>
> DESCRIPTION：镜像描述
>
> STARS：用户评价，反应一个镜像的受欢迎程度
>
> OFFICIAL：是否官方
>
> AUTOMATED：自动构建，表示该镜像由Docker Hub自动构建流程创建的

## 拉取镜像

拉取镜像就是从中央仓库中下载镜像到本地

```
docker pull 镜像名称
```

> 例如，我要下载centos7镜像
>
> ```
> docker pull centos:7
> ```

## 删除镜像

按镜像ID删除镜像

```
docker rmi 镜像ID
```

删除所有镜像

```
docker rmi `docker images -q`
```






