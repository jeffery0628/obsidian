---
Create: 2022年 一月 16日, 星期日 12:47
tags: 
  - Engineering/hadoop
  - 大数据
---
## 基本语法
1. bin/hadoop fs 具体命令
2. bin/hdfs dfs 具体命令

两个命令完全相同


## 命令大全
```bash
bin/hadoop fs


[-appendToFile <localsrc> ... <dst>]
        [-cat [-ignoreCrc] <src> ...]
        [-checksum <src> ...]
        [-chgrp [-R] GROUP PATH...]
        [-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
        [-chown [-R] [OWNER][:[GROUP]] PATH...]
        [-copyFromLocal [-f] [-p] <localsrc> ... <dst>]
        [-copyToLocal [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
        [-count [-q] <path> ...]
        [-cp [-f] [-p] <src> ... <dst>]
        [-createSnapshot <snapshotDir> [<snapshotName>]]
        [-deleteSnapshot <snapshotDir> <snapshotName>]
        [-df [-h] [<path> ...]]
        [-du [-s] [-h] <path> ...]
        [-expunge]
        [-get [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
        [-getfacl [-R] <path>]
        [-getmerge [-nl] <src> <localdst>]
        [-help [cmd ...]]
        [-ls [-d] [-h] [-R] [<path> ...]]
        [-mkdir [-p] <path> ...]
        [-moveFromLocal <localsrc> ... <dst>]
        [-moveToLocal <src> <localdst>]
        [-mv <src> ... <dst>]
        [-put [-f] [-p] <localsrc> ... <dst>]
        [-renameSnapshot <snapshotDir> <oldName> <newName>]
        [-rm [-f] [-r|-R] [-skipTrash] <src> ...]
        [-rmdir [--ignore-fail-on-non-empty] <dir> ...]
        [-setfacl [-R] [{-b|-k} {-m|-x <acl_spec>} <path>]|[--set <acl_spec> <path>]]
        [-setrep [-R] [-w] <rep> <path> ...]
        [-stat [format] <path> ...]
        [-tail [-f] <file>]
        [-test -[defsz] <path>]
        [-text [-ignoreCrc] <src> ...]
        [-touchz <path> ...]
        [-usage [cmd ...]]


```


## 上传
1. ==-moveFromLocal==：从本地剪切并粘贴到HDFS
	```bash
	hadoop fs  -moveFromLocal  ./kongming.txt  /sanguo/shuguo
	```
2. ==-copyFromLocal==:从本地文件系统中拷贝文件到HDFS路径去
	```bash
	hadoop fs -copyFromLocal README.txt /
	```
3. ==appendToFile==:追加一个文件到HDFS已经存在的文件末尾
	```bash
	hadoop fs -appendToFile liubei.txt /sanguo/shuguo/kongming.txt
	```
4. ==-put==:等同于copyFromLocal
	```bash
	hadoop fs -put ./zaiyiqi.txt /user/atguigu/test/
	```

## 下载
1. ==-copyToLocal==：从HDFS拷贝到本地
	```bash
	hadoop fs -copyToLocal /sanguo/shuguo/kongming.txt ./
	```
2. ==-get==：等同于copyToLocal，就是从HDFS下载文件到本地
	```bash
	hadoop fs -get /sanguo/shuguo/kongming.txt ./
	```
3. ==-getmerge==:合并下载多个文件，比如HDFS的目录`/user/xxx/test`下有多个文件：log.1,log.2,log.3...
	```bash
	hadoop fs -getmerge /user/atguigu/test/* ./zaiyiqi.txt
	```
	
## 其余常用操作
1. ==-ls==：显示目录信息
	```bash
	hadoop fs -ls /
	```
2. ==-mkdir==:在HDFS上创建目录
	```bash
	hadoop fs -mkdir -p /sanguo/shuguo
	```
	
3. ==-cat==：显示文件内容
	```bash
	hadoop fs -cat ./kongming.txt
	```
	
4. ==-chgrp==、==-chmod==、==-chown==：Linux文件系统中的用法一样，修改文件所属权限
	```bash
	hadoop fs -chmod 666 kongming.txt
	hadoop fs -chown lizhen:lizhen kongming.txt
	```
5. ==-cp==：从HDFS的一个路径拷贝到HDFS的另一个路径
	```bash
	hadoop fs -cp ./kongming.txt /test
	```
6. ==-mv==：在HDFS目录中移动文件
	```bash
	hadoop fs -mv /kongming.txt /sanguo
	```
7. ==-tail==： 显示一个文件的末尾
	```bash
	hadoop fs -tail ./kongming.txt
	```
	
8. ==-rm==:删除文件或文件夹
	```bash
	hadoop fs -rm /user/xxx/test
	```
9. ==-rmdir==:删除空目录
	```bash
	hadoop fs -mkdir /user/xxx/test
	hadoop fs -rmdir /user/xxx/test
	```
	
10. ==-du==：统计文件夹的大小信息
	```bash
	hadoop fs -du -s -h ./
	```

11. ==-setrep==：设置HDFS中文件的副本数量
	```bash
	hadoop fs -settrep 10 /kongming.txt
	```
	>这里设置的副本数只是记录在NameNode的元数据中，是否真的会有这么多副本，还得看DataNode的数量。因为目前只有3台设备，最多也就3个副本，只有节点数的增加到10台时，副本数才能达到10。

