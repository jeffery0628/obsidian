---
Create: 2021年 十月 25日, 星期一 13:13
Linked Areas: 
Linked Project:
Linked Tools: 
Other  Links: 
tags: 
  - Engineering/java
  - 大数据

---
# JDK、JRE、JVM

## Java虚拟机——JVM

`JVM（Java Virtual Machine ）`：Java虚拟机，简称JVM，是运行所有Java程序的假想计算机，是Java程序的运行环境，是Java 最具吸引力的特性之一。我们编写的Java代码，都运行在 JVM 之上。 跨平台：任何软件的运行，都必须要运行在操作系统之上，而我们用Java编写的软件可以运行在任何的操作系统上，这个特性称为Java语言的跨平台特性。该特性是由JVM实现的，我们编写的程序运行在JVM上，而JVM 运行在操作系统上。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-basic/image-20200905091050620.png)

如图所示，Java的虚拟机本身不具备跨平台功能的，每个操作系统下都有不同版本的虚拟机。

## JRE 和 JDK

`JRE (Java Runtime Environment)` ：是Java程序的运行时环境，包含 JVM 和运行时所需要的核心类库 。
`JDK (Java Development Kit)`：是Java程序开发工具包，包含 JRE 和开发人员使用的工具。
想要运行一个已有的Java程序，那么只需安装 JRE 即可。
想要开发一个全新的Java程序，必须安装 JDK 。

