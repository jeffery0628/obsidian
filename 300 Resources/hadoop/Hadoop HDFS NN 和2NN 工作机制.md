---
Create: 2022年 一月 16日, 星期日 15:31
tags: 
  - Engineering/hadoop
  - 大数据
---


## NN 和 2NN工作机制
==思考==：NameNode中的元数据是存储在哪里的？
首先，做个假设，如果存储在NameNode节点的磁盘中，因为经常需要进行随机访问，还有响应客户请求，必然是效率过低。因此，元数据需要存放在内存中。但如果只存在内存中，一旦断电，元数据丢失，整个集群就无法工作了。==因此产生在磁盘中备份元数据的FsImage==。

这样又会带来新的问题，当在内存中的元数据更新时，如果同时更新FsImage，就会导致效率过低，但如果不更新，就会发生一致性问题，一旦NameNode节点断电，就会产生数据丢失。==因此，引入Edits文件(只进行追加操作，效率很高)。每当元数据有更新或者添加元数据时，修改内存中的元数据并追加到Edits中==。这样，一旦NameNode节点断电，可以通过FsImage和Edits的合并，合成元数据。

但是，如果长时间添加数据到Edits中，会导致该文件数据过大，效率降低，而且一旦断电，恢复元数据需要的时间过长。因此，需要定期进行FsImage和Edits的合并，如果这个操作由NameNode节点完成，又会效率过低。==因此，引入一个新的节点SecondaryNamenode，专门用于FsImage和Edits的合并==。

![[700 Attachments/Pasted image 20220116154231.png]]

第一阶段：NameNode启动
1. 第一次启动NameNode格式化后，创建Fsimage和Edits文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存。
2. 客户端对元数据进行增删改的请求。
3. NameNode记录操作日志，更新滚动日志。
4. NameNode在内存中对元数据进行增删改。


第二阶段：Secondary NameNode工作
1. Secondary NameNode询问NameNode是否需要CheckPoint。直接带回NameNode是否检查结果。
2. Secondary NameNode请求执行CheckPoint。
3. NameNode滚动正在写的Edits日志。
4. 将滚动前的编辑日志和镜像文件拷贝到Secondary NameNode。
5. Secondary NameNode加载编辑日志和镜像文件到内存，并合并。
6. 生成新的镜像文件fsimage.chkpoint。
7. 拷贝fsimage.chkpoint到NameNode。
8. NameNode将fsimage.chkpoint重新命名成fsimage。
