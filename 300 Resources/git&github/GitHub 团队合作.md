---
Create: 2021年 十二月 26日, 星期日 12:17
tags: 
  - Engineering/git

---

## 团队成员邀请

1. 打开github上对应代码仓库
2. 找到setting
3. manage access
4. add people





![[700 Attachments/Pasted image 20211226121928.png]]
![[700 Attachments/Pasted image 20211226122013.png]]

> 可以将邀请链接发送给对方![[700 Attachments/Pasted image 20211226122233.png]] ，等待对方同意接受邀请



## 团队成员操作命令
### 拉取
pull = fetch + merge

```git
git fetch [远程库地址别名] [远程分支名] 
git merge [远程库地址别名/远程分支名] 
git pull [远程库地址别名] [远程分支名]
```

### 解决冲突
1. 如果不是基于GitHub远程库的最新版所做的修改，不能推送，必须先拉取
2. 拉取下来后，如果进入冲突状态，则按照 git冲突解决方法即可


