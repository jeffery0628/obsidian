---
Create: 2021年 十二月 26日, 星期日 11:50
tags: 
  - Engineering/git

---

## 哈希
![[700 Attachments/Pasted image 20211226115139.png]]

哈希是一个系列的加密算法，各个不同的哈希算法虽然加密强度不同，但是有以下 几个共同点：
1. 不管输入数据的数据量有多大，输入同一个哈希算法，得到的加密结果长度固定。
2. 哈希算法确定，输入数据确定，输出数据能够保证不变
3. 哈希算法确定，输入数据有变化，输出数据一定有变化，而且通常变化很大
4. 哈希算法不可逆

 Git 底层采用的是 SHA-1 算法。
 哈希算法可以被用来验证文件。原理如下图所示：
![[700 Attachments/Pasted image 20211226115247.png]]

Git 就是靠这种机制来从根本上保证数据完整性的。



##  Git 保存版本的机制
      
### 集中式版本控制工具的文件管理机制
以文件变更列表的方式存储信息。这类系统将它们保存的信息看作是一组基本文件和每个文件随时间逐步累积的差异。
![[700 Attachments/Pasted image 20211226115502.png]]

### Git 的文件管理机制
Git 把数据看作是小型文件系统的一组快照。每次提交更新时 Git 都会对当前 的全部文件制作一个快照并保存这个快照的索引。为了高效，如果文件没有修改， Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。所以 Git 的 工作方式可以称之为快照流。
![[700 Attachments/Pasted image 20211226115622.png]]

### git 文件管理机制细节
git的“提交对象”
![[700 Attachments/Pasted image 20211226115725.png]]

提交对象及其福对象形成的链条
![[700 Attachments/Pasted image 20211226115748.png]]


## Git 分支管理机制
### 分支的创建
![[700 Attachments/Pasted image 20211226115945.png]]


### 分支的切换
![[700 Attachments/Pasted image 20211226115959.png]]
![[700 Attachments/Pasted image 20211226120027.png]]
![[700 Attachments/Pasted image 20211226120037.png]]
![[700 Attachments/Pasted image 20211226120045.png]]