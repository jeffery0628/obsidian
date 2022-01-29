---
Create: 2022年 一月 20日, 星期四 22:32
tags: 
  - Engineering/zookeeper
  - 大数据
---

## 客户端命令行操作

| 命令基本语法 | 功能描述                                                     |
| ------------ | ------------------------------------------------------------ |
| help         | 显示所有操作命令                                             |
| ls path      | 使用 ls 命令来查看当前znode的子节点  -w 监听子节点变化  -s  附加次级信息 |
| create       | 普通创建  -s 含有序列  -e 临时（重启或者超时消失）           |
| get path     | 获得节点的值  -w 监听节点内容变化  -s  附加次级信息          |
| set          | 设置节点的具体值                                             |
| stat         | 查看节点状态                                                 |
| delete       | 删除节点                                                     |
| deleteall    | 递归删除节点                                                 |


1. 启动客户端：bin/zkCli.sh
2. 显示所有操作命令：help
3. 查看当前znode中所包含的内容：ls /
4. 查看当前节点详细数据：ls2 /
5. 分别创建2个普通节点
	```bash
	create /sanguo "jinlian"
	create /sanguo/shuguo "liubei"
	```
6. 获得节点的值:
```bash
get /sanguo
get /sanguo/shuguo
```
7. 创建短暂节点
	```bash
	create -e /sanguo/wuguo "zhouyu"
	```
	1. 在当前客户端是能查看到的
		```bash
		ls /sanguo 
		[wuguo, shuguo]
		```
	2. 退出当前客户端然后再重启客户端
		```bash
		quit
		
		bin/zkCli.sh
		```
	3. 再次查看根目录下短暂节点已经删除：ls /sanguo
8. 创建带序号的节点
	1. 先创建一个普通的根节点/sanguo/weiguo
		```bash
		create /sanguo/weiguo "caocao"
		```
	1. 创建带序号的节点:如果原来没有序号节点，序号从0开始依次递增。如果原节点下已有2个节点，则再排序时从2开始，以此类推。
		```bash
		create -s /sanguo/weiguo/xiaoqiao "jinlian"
		Created /sanguo/weiguo/xiaoqiao0000000000
		create -s /sanguo/weiguo/daqiao "jinlian"
		Created /sanguo/weiguo/daqiao0000000001
		create -s /sanguo/weiguo/diaocan "jinlian"
		Created /sanguo/weiguo/diaocan0000000002
		```
9. 修改节点数据值
	```bash
	set /sanguo/weiguo "simayi"
	```
10. 节点的值变化监听
	1. 在hadoop104主机上注册监听/sanguo节点数据变化:get /sanguo watch
	2. 在hadoop103主机上修改/sanguo节点的数据: set /sanguo "xisi"
	3. 观察hadoop104主机收到数据变化的监听
11. 节点的子节点变化监听（路径变化）
	1. 在hadoop104主机上注册监听/sanguo节点的子节点变化:ls /sanguo watch
	2. 在hadoop103主机/sanguo节点上创建子节点:create /sanguo/jin "simayi"
	3. 观察hadoop104主机收到子节点变化的监听
12. 删除节点:delete /sanguo/jin
13. 递归删除节点: rmr /sanguo/shuguo
14. 查看节点状态:stat /sanguo





