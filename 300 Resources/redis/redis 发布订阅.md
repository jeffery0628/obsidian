---
Create: 2021年 十二月 14日, 星期二 13:12
tags: 
  - Engineering/redis
  - 大数据
---
## 简介
进程间的一种消息通信模式：发送者(pub)发送消息，订阅者(sub)接收消息。
![[700 Attachments/720 pictures/Pasted image 20211214131354.png]]

## 命令
1. Psubscribe ： ==订阅==一个或多个符合给定模式的频道，PSUBSCRIBE pattern \[pattern ...\]
2. pubsub: 查看订阅与发布系统状态。PUBSUB subcommand \[argument \[argument ...\]\]
3. publish: 将信息发送到指定的频道, PUBLISH channel message
4. punsubscribe : 退订所有给定模式的频道。PUNSUBSCRIBE \[pattern \[pattern ...]]
5. subscribe: 订阅给定的一个或多个频道的信息。SUBSCRIBE channel \[channel ...\]\]
6. unsubscribe: 指退订给定的频道。UNSUBSCRIBE \[channel \[channel ...]]

>  先订阅后发布 后才能收到消息
>  1.可以一次性订阅多个，SUBSCRIBE c1 c2 c3
>  2. 消息发布，PUBLISH c2 hello-redis
>  3. 订阅多个，通配符*， PSUBSCRIBE new* 
>  4. 收取消息， PUBLISH new1 redis2015 

