---
Create: 2021年 十二月 4日, 星期六 23:31
tags: 
  - linux

---


# 添加用户
1. useradd username: 添加新用户
2. useradd -g group_name username  : 添加新用户到某个组
3. passwd username ： 设置用户密码


# 查看用户是否存在
id username


# 查看当前系统创建了哪些用户
cat /etc/passwd

# 切换用户
1. su username ：             切换用户，只能获得用户的执行权限，不能获得环境变量
2. su - username ：             切换到用户并获得该用户的环境变量及执行权限

# 删除用户
userdel username ：             删除用户但保存用户主目录
userdel -r username ：             用户和用户主目录，都删除

# 查看登陆用户信息
1. whoami ：             显示自身用户名称
2. who am i ：             显示**登录用户**的用户名


# 给普通用户添加root权限
修改配置文件： /etc/sudoers
```
username ALL=(ALL) ALL
```

# 修改用户
usermod -g group_name username：             修改用户的初始登录组，给定的组必须存在



