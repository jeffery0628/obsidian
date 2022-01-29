---
Create: 2021年 十二月 27日, 星期一 13:45
tags: 
  - Engineering/MySql
  - 大数据
---


![[700 Attachments/Pasted image 20211227134550.png]]
此时查看，多了很多进程：
``` bash
ps -ef | grep mysql
```
![[700 Attachments/Pasted image 20211227134629.png]]
尝试去登录或者操作：报错！
![[700 Attachments/Pasted image 20211227134641.png]]
查看服务状态：
![[700 Attachments/Pasted image 20211227134652.png]]

            

==解决==：杀死所有和mysql进程相关的操作，然后重启服务！
```bash
killall mysqld
```
![[700 Attachments/Pasted image 20211227134723.png]]
> 注意是mysqld，d代表demon，守护进程。

然后再重启：
```bash
service msyql start
```

