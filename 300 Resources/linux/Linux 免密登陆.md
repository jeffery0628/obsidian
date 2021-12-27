---
Create: 2021年 十二月 11日, 星期六 18:36
tags: 
  - linux

---

# 客户端生成公私钥
本地客户端生成公私钥：（一路回车默认即可）
```
ssh-keygen
```
会在~/.ssh下生成 id_rsa （私钥）和  id_rsa.pub (公钥)
# 上传公钥到服务器
```
ssh-copy-id -i ~/.ssh/id_rsa.pub lizhen@192.168.0.114
```
这条命令是写到服务器上的ssh目录下
通过authorized_keys 可以看到客户端写入到服务器的 id_rsa.pub （公钥）内容。
