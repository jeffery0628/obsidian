---
Create: 2021年 十月 27日, 星期三 23:08
tags: 
  - Engineering/java
  - 大数据
---
# 链表

`链表(linked list)`:,由一系列结点node组成，结点可以在运行时动态生成。每个结点包括两个部分：一个是存储数据元素的数据域，另一个是存储下一个结点地址的指针域。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E5%8D%95%E9%93%BE%E8%A1%A8%E7%BB%93%E6%9E%84%E7%89%B9%E7%82%B9.png)

采用该结构的集合，对元素的存取有如下的特点：

-   多个结点之间，通过地址进行连接。
    
    ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E5%8D%95%E9%93%BE%E8%A1%A8%E7%BB%93%E6%9E%84.png)
    
-   查找元素慢：想查找某个元素，需要通过连接的节点，依次向后查找指定元素
    
-   增删元素快：
    
    增加元素：只需要修改连接下个元素的地址即可。
    
    ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E5%A2%9E%E5%8A%A0%E7%BB%93%E7%82%B9.png)
    
    删除元素：只需要修改连接下个元素的地址即可。
    
    ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E5%88%A0%E9%99%A4%E7%BB%93%E7%82%B9.bmp)



