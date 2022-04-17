---
Create: 2022年 四月 10日, 星期日 12:38
tags: 
  - Engineering/scala
  - 大数据
---

# scala 和 spark

1. spark 是新一代内存级大数据计算框架，是大数据的重要内容。
2. spark就是scala编写的。因此为了更好的学习Spark，需要掌握scala这门语言。
3. spark 的兴起，带动scala语言的发展。

# scala 发展历史
联邦理工学院的Martin Odersky 于2001年设计Scala。
Martin Odersky是编译器及编程的狂热爱好者，长时间的编程后，希望发明一种语言，能够让写程序这样的基础工作变得高效，简单。所以当接触到JAVA语言后，对JAVA这门便携式，运行在网络，且存在==垃圾回收==的语言产生了极大的兴趣，所以决定将函数式编程语言的特点融合到JAVA中，由此发明了两种语言（Pizza 和 Scala）。
Pizza 和 scala 极大地推动了Java 编程语言的发展。
- JDK5.0的泛型、增强for循环、自动类型转换等，都是从Pizza引入的新特性。
- JDK8.0 的类型推断、Lambda表达式就是从Scala引入的特性。

# java 和 scala
一般来说，学Scala的人，都会Java，而Scala是基于Java的，因此我们需要将Scala和Java以及JVM之间的关系搞清楚。
![[700 Attachments/Pasted image 20220410124839.png]]

# scala 特点

1.  scala 是一门以JVM为运行环境并将面向对象和函数式编程的最佳特性结合在一起的静态类型编程语言。
    
2.  scala是一门多范式的编程语言。（多范式，就是多种编程方法的意思，有面向过程、面向对象、泛型、函数式四种程序设计方法。）
    
3.  scala源代码(.scala)会被编译成java字节码(.class)，然后运行与JVM之上，并可以调用现有的Java类库，实现两种语言的无缝对接。
    
4.  简洁高效。


