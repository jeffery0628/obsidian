---
Create: 2021年 十二月 25日, 星期六 21:53
tags: 
  - Engineering/git
---

## 本地库初始化
```git
git init
```
查看.git 目录：
![[700 Attachments/720 pictures/Pasted image 20211225230943.png]]
>  注意：.git 目录中存放的是本地库相关的子目录和文件，不要删除，也不要胡 乱修改。

## 设置签名
形式：
- 用户名：tom  
- email: xx@gmail.com

作用：用以区分不同开发人员的身份
> 这里设置的签名和登录远程库(代码托管中心)的账号、密码没有任何关 系。

### 项目级别签名
项目级别签名仅在当前本地库范围内有效,签名信息保存在项目目录下.git/config文件中
```git
git config user.name tom_pro
git config user.email goodMorning_pro@atguigu.com
```
![[700 Attachments/720 pictures/Pasted image 20211225231741.png]]
### 系统用户级别签名
系统用户级别：当前操作系统的用户范围,签名信息保存在home目录下:~/.gitconfig文件中
```git
git config --global user.name tom_glb
git config --global goodMorning_pro@atguigu.com  信息保存位置：~/.gitconfig 文件
```
![[700 Attachments/720 pictures/Pasted image 20211225231948.png]]

### 签名优先级比较
级别优先级
- 就近原则：项目级别优先于系统用户级别，二者都有时采用项目级别的签名
- 如果只有系统用户级别的签名，就以系统用户级别的签名为准
- 二者都没有不允许

## 基本操作
### 状态查看
查看工作区、暂存区状态
```git
git status
```

### 添加
将工作区的 “新建/修改” 添加到暂存区
```git
git add [filename]

```

### 提交
将暂存区的内容提交到本地库

```git
git commit -m "commit message"  [filename]

```

### 查看历史记录
git log 如果本地库提交的历史记录过多，可以通过
- 空格向下翻页
- b 向上翻页 
- q 退出

来进行查看

```git
git log
git log --pretty=oneline
git log --oneline
```
![[700 Attachments/720 pictures/Pasted image 20211225232922.png]]

![[700 Attachments/720 pictures/Pasted image 20211225233257.png]]

![[700 Attachments/720 pictures/Pasted image 20211225233333.png]]

> HEAD@{从当前版本移动到这个版本需要多少步}

### 索引操作：历史版本的前进后退
#### 基于索引值操作
```git
git reset --hard [局部索引值]
git reset --hard 161ce91

```
#### 使用^符号
使用\^符号只能基于HEAD指针版本向历史方向移动
```git
git reset --hard HEAD^
```
> 一个\^表示后退一步，n个表示后退n步。

#### 使用\~符号
使用\~符号也表示只能后退
```git
git reset --hard HEAD~n
```
> 表示后退n步

#### 索引操作小结：reset命令的三个参数对比
--soft参数： 仅在本地库移动HEAD指针，工作区和暂存区修改过的内容不会丢失
![[700 Attachments/720 pictures/Pasted image 20211225234635.png]]

--mix参数：在本地库移动HEAD指针，但是对于当前从工作区提交到暂存区的内容会被丢失重置
![[700 Attachments/720 pictures/Pasted image 20211225234800.png]]

--hard参数： 在本地库移动HEAD指针，当前工作区和提交到暂存区的内容都会被重置。


### 删除文件找回
==前提==：删除前，文件存在时的状态提交到了本地库。
```git
git reset --hard [指针位置]
```
> 删除操作已经提交到本地库：指针位置指向历史记录
> 删除操作尚未提交到本地库：指针位置使用HEAD

### 比较文件差异

==git diff [filename]== : 将工作区中的文件和暂存区进行比较
==git diff [本地库中历史版本] [文件名] == : 将工作区中的文件和本地库历史记录比较

> 不带文件名比较多个文件。











