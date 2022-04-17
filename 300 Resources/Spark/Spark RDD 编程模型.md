---
Create: 2022年 三月 11日, 星期五 10:10
tags: 
  - Engineering/spark
  - 大数据
---

# 编程模式
在Spark中，RDD被表示为对象，通过对象上的方法调用来对RDD进行转换。RDD经过一系列的transformations转换定义之后，就可以调用actions触发RDD的计算，action可以是向应用程序返回结果，或者是向存储系统保存数据。在Spark中，只有遇到action，才会执行RDD的计算（即延迟计算）。
![[700 Attachments/Pasted image 20220311101243.png]]



