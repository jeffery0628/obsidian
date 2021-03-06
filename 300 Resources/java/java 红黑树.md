---
Create: 2021年 十月 27日, 星期三 23:10
tags: 
  - Engineering/java
  - 大数据
---
# 红黑树

`二叉树(binary tree)`: ,是每个结点不超过2的有序**树（tree）** 。

二叉树是每个节点最多有两个子树的树结构。顶上的叫根结点，两边被称作“左子树”和“右子树”。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E4%BA%8C%E5%8F%89%E6%A0%91.bmp)

**红黑树**是二叉树的一种比较有意思的树，红黑树本身就是一颗二叉查找树，将节点插入后，该树仍然是一颗二叉查找树。也就意味着，树的键值仍然是有序的。

红黑树的约束:

1.  节点可以是红色的或者黑色的
    
2.  根节点是黑色的
    
3.  叶子节点(特指空节点)是黑色的
    
4.  每个红色节点的子节点都是黑色的
    
5.  任何一个节点到其每一个叶子节点的所有路径上黑色节点数相同
    

红黑树的特点:

 速度特别快,趋近平衡树,查找叶子元素最少和最多次数不多于二倍

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]

