---
Create: 2021年 十月 27日, 星期三 23:07
tags: 
  - Engineering/java
  - 大数据
---






# 数组

`数组(Array)`:是有序的元素序列，数组是在内存中开辟一段连续的空间，并在此空间存放元素。

简单的说,采用该结构的集合，对元素的存取有如下的特点：

-   查找元素快：通过索引，可以快速访问指定位置的元素
    
    ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E6%95%B0%E7%BB%84%E6%9F%A5%E8%AF%A2%E5%BF%AB.png)
    
-   增删元素慢
    
    指定索引位置增加元素：需要创建一个新数组，将指定新元素存储在指定索引位置，再把原数组元素根据索引，复制到新数组对应索引的位置。如下图
    
    ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E6%95%B0%E7%BB%84%E6%B7%BB%E5%8A%A0.png)
    
    指定索引位置删除元素：需要创建一个新数组，把原数组元素根据索引，复制到新数组对应索引的位置，原数组中指定索引位置元素不复制到新数组中。如下图
    
    ![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-data-structure/%E6%95%B0%E7%BB%84%E5%88%A0%E9%99%A4.png)
	
	

[[300 Resources/java/java 数组|java 数组]]
[[200 Areas/230 Engineering/231 java|java 目录]]