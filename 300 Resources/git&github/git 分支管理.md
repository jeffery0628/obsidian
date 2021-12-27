---
Create: 2021年 十二月 26日, 星期日 11:41
tags: 
  - Engineering/git

---

## 简介
分支管理是指：在版本控制过程中，使用多条线同时推进多个任务。
![[700 Attachments/Pasted image 20211226114254.png]]

### 优点
1. 同时并行推进多个功能开发，提高开发效率
2. 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。


### 操作

创建分支
```git
git branch [分支名]
```

查看分支
```git
git branch -v
```

切换分支
```git
git checkout [分支名]
```

合并分支
1. 切换到接受修改的分支(被合并，增加新内容)上，git checkout \[被合并分支名\]
2. 执行合并操作，git merge \[被合并的分支名\]
3. 解决冲突
	1. 冲突的表现：![[700 Attachments/Pasted image 20211226114801.png]]
	2. 冲突的解决：
		1. 编辑文件，删除特殊符号
		2. 根据需求，删除引起冲突的部分内容
		3. git add \[文件名\]
		4. git commit -m 'resolve conflict' .(==注意==：此时的commit一定不能带有具体文件名)





