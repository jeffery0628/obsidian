---
Create: 2021年 十月 27日, 星期三 13:27
tags: 
  - Engineering/java
  - 大数据
---

Java并不是纯面向对象的语言。Java语言是一个面向对象的语言，但是Java中的基本数据类型却是不面向对象的。基本数据类型有它的优势：性能（效率高，节省空间）。

但是我们在实际使用中经常需要将基本数据类型转化成对象，便于操作。比如：（1）集合的操作，（2）使用Object类型接收任意类型的数据等，（3）泛型实参，这时，就需要将基本数据类型数据转化为对象。

包装类均位于java.lang包，包装类和基本数据类型的对应关系

| 基本数据类型 | 包装类    |
| ------------ | --------- |
| boolean      | Boolean   |
| byte         | Byte      |
| short        | Short     |
| int          | Integer   |
| long         | Long      |
| char         | Character |
| float        | Float     |
| double       | Double    |

## 自动装箱-拆箱

基本数据类型就自动的封装到与它相同类型的包装中，如：

```java
Integer i = 100;
```

本质上是，编译器编译时为我们添加了：

```java
Integer i = new Integer(100);
```

包装类对象自动转换成基本类型数据。如：

```java
int a = new Integer(100);
```

本质上，编译器编译时为我们添加了：

```java
int a = new Integer(100).intValue();
```

## 包装类的作用

### 数据类型的范围

MIN_VALUE、MAX_VALUE

Float和Double中还有正无穷大POSITIVE_INFINITY、负无穷大NEGATIVE_INFINITY，以及NaN，是Not a Number的缩写。NaN 用于处理计算中出现的错误情况，比如 0.0 除以 0.0 或者求负数的平方根。

### 数据类型的转换

字符串转成包装类对象

1. 使用包装类型的构造方法，如：`Integer t = new Integer("500");`
2. 使用包装类的valueOf方法，如：`Integer i=Integer.valueOf("500");`

字符串转成基本数据类型

1. 通过包装类的`parseXxx(String s)`静态方法,如：`int i=Integer.parseInt("500");`



### 包装类的其他方法

Integer类型

```java
public static String toBinaryString(int i)  //把十进制转成二进制
public static String toHexString(int i)   //把十进制转成十六进制
public static String toOctalString(int i)  //把十进制转成八进制
```

Character类型

```java
public static char toUpperCase(char ch)  //转成大写字母
public static char toLowerCase(char ch)  //转成小写字母
```

equals

按照包装的基本数据类型的值比较

compareTo

按照包装的基本数据类型的值比较



### 缓存

在编程时大量需要值在-128到127范围之间的Integer对象。

如果只能通过new来创建，需要在堆中开辟大量值一样的Integer对象。这是相当不划算的，IntegerCache.cache很好的起到了缓存的作用。



```java
byte Byte -128–127
short Short -128–127
int Integer -128—127
long Long -128—127
float Float 不缓存
double Double 不缓存
char Character 0–127
boolean Boolean TURE，FALSE
```

# 
