---
Create: 2021年 十月 25日, 星期一 13:18
Linked Areas: 
Linked Project:
Linked Tools: 
Other  Links: 
tags: 
  - Engineering/java
  - 大数据
---
# 编写、编译、运行

Java程序开发三步骤：编写、编译、运行。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-basic/image-20200905091503808.png)

## 编写

> java源文件名必须和java中类名保持一致。
> 
> 一个.java源文件中可以有多个类，但是**只能有一个public类**, 而且如果有public类的话，这个文件的名字要和这个类的名字一样。如果都没有public类，名字可以不和这个类一样。

## 编译

是指将编写的Java源文件翻译成JVM认识的class文件，在这个过程中， javac 编译器会检查所编写的程序是否有错误，有错误就会提示出来，如果没有错误就会编译成功。

 javac Demo.java

## 运行

是指将 class文件交给JVM去运行，此时JVM就会去执行编写的程序。

 java Demo




[[200 Areas/230 Engineering/232 大数据/java|java 目录]]