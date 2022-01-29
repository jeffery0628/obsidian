---
Create: 2022年 一月 16日, 星期日 11:37
tags: 
  - Engineering/hadoop
  - 大数据
---
![[700 Attachments/Pasted image 20220116113900.png]]
1. NameNode(NN):就是Master，它是一个主管、管理者
	1. 管理HDFS的名称空间
	2. 配置副本策略
	3. 管理数据块(Block)映射信息
	4. 处理客户端读写请求
2. DataNode(DN)：就是Slave。NameNode下达命令，DataNode执行实际的操作
	1. 存储实际的数据块
	2. 执行数据块的读/写操作
3. Client：客户端
	1. 文件切分，文件上传HDFS的时候，Client将文件切分成一个一个的Block，然后进行上传；
	2. 与NameNode交互，获取文件的位置信息；
	3. 与DataNode交互，读取或写入数据；
	4. Clent提供一些命令来管理HDFS，比如NameNode格式化；
	5. Client可以通过一些命令来访问HDFS，比如对HDFS增删改查操作；
4. Secondary NameNode：并非NameNode的热备。当NameNode挂掉的时候，它并不能马上替换NameNode并提供服务。
	1. 辅助NameNode，分担其工作量，比如定期合并Fsimage和Edits，并推送给NameNode；
	2. 在紧急情况下，可辅助恢复NameNode。






