---
Create: 2021年 十月 26日, 星期二 09:02
Linked Areas: 
Linked Project:
Linked Tools: [[500 Tools/jetbrain/IDEA]]
Other  Links: https://mp.weixin.qq.com/s/2h-k7C5ILJCI4gYTdVvYhA
tags: 
  - Jetbrain
  - IDEA
---





## 查看图形形式的继承链
在你想查看的类的标签页内，点击**右键，选择 Diagrams**，其中有 show 和 show ... Popup，只是前者新建在标签页内，后者以浮窗的形式展示：

![图片](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640)

实际上，也可以从左边的项目目录树中，对你想查看的类点击右键，同样选择 Diagrams，效果是一样的：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091051355)

然后就会得到如下图所示的继承关系图形，以自定义的 Servlet 为例：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091114666)

显而易见的是：

- **蓝色实线箭头** 是指继承关系
- **绿色虚线箭头** 是指接口实现关系



## 优化继承链图形

### 去掉不关心的类

得到的继承关系图形，有些并不是我们想去了解的，比如上图的 Object 和 Serializable，我们只想关心 Servlet 重要的那几个继承关系，怎么办？

简单，删掉。点击选择你想要删除的类，然后直接使用键盘上的 delete 键就行了。清理其他类的关系后图形如下：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091234960)



### 展示类的详细信息

在**页面点击右键，选择 show categories**，根据需要可以展开类中的属性、方法、构造方法等等。当然，第二种方法**也可以直接使用上面的工具栏**：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091502819)

然后你就会得到：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091525332)

什么，方法里你还想筛选，比如说想看 protected 权限及以上范围的？简单，**右键选择 Change Visibility Level**，根据需要调整即可。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091551716)

什么，你嫌图形太小你看不清楚？IDEA 也可以满足你，**按住键盘的 Alt，竟然出现了放大镜**，惊不惊喜，意不意外？

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091638777)

### 加入其他类到关系中来

当我们还需要查看其他类和当前类是否有继承上的关系的时候，我们可以选择加其加入到当前的继承关系图形中来。

在**页面点击右键，选择 Add Class to Diagram**，然后输入你想加入的类就可以了：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091723452)

例如我们添加了一个 Student 类，如下图所示。好吧，并没有任何箭头，看来它和当前这几个类以及接口并没有发生什么不可描述的关系：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026091748816)

### 查看具体代码

如果你想查看某个类中，比如某个方法的具体源码，当然，不可能给你展现在图形上了，不然屏幕还不得撑炸？

但是可以利用图形，或者配合 IDEA 的 structure 方便快捷地进入某个类的源码进行查看。

**双击某个类后，你就可以在其下的方法列表中游走，对于你想查看的方法，选中后点击右键，选择 Jump to Source：**

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026092046823)

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026092059233)

在进入某个类后，如果还想快速地查看该类的其他方法，还可以利用 IDEA 提供的 structure 功能：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/IDEA%20%20%E7%9C%8B%E6%BA%90%E7%A0%81/640-20211026092122439)

选择左侧栏的 structure 之后，如上图左侧会展示该类中的所有方法，点击哪个方法，页面内容就会跳转到该方法部分去。
