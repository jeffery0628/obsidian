---
Create: 2021年 十二月 26日, 星期日 12:02
tags: 
  - Engineering/git

---

## 创建远程仓库
![[700 Attachments/Pasted image 20211226120533.png]]


## 本地库和远程库关联
### 查看当前所有远程库地址别名
```git
git remote -v  # 查看当前所有远程地址别名

git remote add [别名] [远程地址]

```

![[700 Attachments/Pasted image 20211226120944.png]]


### 将本地库内容推送到远程分支
git push \[远程库别名\] \[远程库的分支名\]

### 克隆
```git
git clone \[远程地址\]
```

> 克隆将会完整的吧远程库下载到本地
> 创建origin 远程地址别名
> 初始化本地库



