---
Create: 2021年 十月 27日, 星期三 13:34
tags: 
  - Engineering/java
  - 大数据
---

# 字符串类String

`java.lang.String`类代表字符串。Java程序中所有的字符串文字（例如 "abc" ）都可以被看作是实现此类的实例。

类 `String` 中包括用于检查各个字符串的方法，比如用于比较字符串，搜索字符串，提取子字符串以及创建具有翻译为大写或小写的所有字符的字符串的副本。

## 特点

1. 字符串的内容永不可变。【重点】
2. 正是因为字符串不可改变，所以字符串是可以共享使用的。
3. 字符串效果上相当于是`char[]`字符数组，但是底层原理是`byte[]`字节数组。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-common-classes/01-%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E5%B8%B8%E9%87%8F%E6%B1%A0.png)

```java
String s1 = "abc"; 
s1 += "d"; 
System.out.println(s1); // "abcd" 
// 内存中有"abc"，"abcd"两个对象，s1从指向"abc"，改变指向，指向了"abcd"。

String s1 = "abc"; 
String s2 = "abc"; 
// 内存中只有一个"abc"对象被创建，同时被s1和s2共享。

String str = "abc";
//相当于：
char data[] = {'a', 'b', 'c'}; 
String str = new String(data); 
// String底层是靠字符数组实现的。
```

## 构造方法

`java.lang.String` ：此类不需要导入。

三种构造方法：

`public String() `：创建一个空白字符串，不含有任何内容。

`public String(char[] value) `：根据字符数组的内容，来创建对应的字符串。

`public String(byte[] bytes) `：通过使用平台的默认字符集解码当前参数中的字节数组来构造新的 String。

```java
// 无参构造 
String str = new String();

// 通过字符数组构造 
char chars[] = {'a', 'b', 'c'}; 
String str2 = new String(chars);

// 通过字节数组构造 
byte bytes[] = { 97, 98, 99 }; 
String str3 = new String(bytes);
```

一种`直接创建`：

```java
String str = "Hello"; // 右边直接用双引号

// 注意：
// String s = new String('abc'); //编译错误
```

### 常用方法

#### 判断功能

`public boolean equals (Object anObject) `：将此字符串与指定对象进行比较。

`public boolean equalsIgnoreCase (String anotherString) `：将此字符串与指定对象进行比较，忽略大小写。

#### 获取功能

`public int length () `：返回此字符串的长度。

`public String concat (String str) `：将指定的字符串连接到该字符串的末尾。

`public char charAt (int index) `：返回指定索引处的 `char`值。

`public int indexOf (String str) `：返回指定子字符串第一次出现在该字符串内的索引。

#### 截取功能

`public String substring (int beginIndex) `：返回一个子字符串，从`beginIndex`开始截取字符串到字符

串结尾。

`public String substring (int beginIndex, int endIndex) `：返回一个子字符串，从`beginIndex`到

`endIndex`截取字符串。含`beginIndex`，不含`endIndex`。

#### 转换功能的

`public char[] toCharArray () `：将此字符串转换为新的字符数组。

`public byte[] getBytes () `：使用平台的默认字符集将该 String编码转换为新的字节数组。

`public String replace (CharSequence target, CharSequence replacement) `：将与`target`匹配的字符串使用`replacement`字符串替换。

#### 分割功能

`public String[] split(String regex) `：将此字符串按照给定的`regex`（规则）拆分为字符串数组。

#### 拼接功能

`public String concat(String str)`：将当前字符串和参数字符串拼接成为返回值新的字符串。



## String、StringBuilder、StringBuffer

String是只读字符串，所引用的字符串不能被改变，一经定义，无法再增删改。
String 定义的字符串保存在常量池里面，进行+操作时不能直接在原有基础上拼接。每次+操作 ： 隐式在堆上new了一个跟原字符串相同的StringBuilder对象，再调用append方法拼接+后面的字符。

```java
String str1="ss";
str1=str1+"oo";
```

等于


```java
StringBuilder str2=new StringBuilder("ss");
str2.append("oo");
```

区别:

`String`是只读字符串，所引用的字符串不能被改变，`Stringbuffer`和`Stringbuilder`定义的可以通过各种方法来达到简单的增删改；

`String`和`Stringbuilder`在`单线程`环境下使用；

`StringBuffer`在`多线程`环境下使用，可以保证线程同步；

`Stringbuilder `和`StringBuffer` 实现方法类似，均表示可变字符序列，不过`StringBuffer` 用`synchronized`关键字修饰（保证线程同步）

**运行速度**:当需要对某一字符串大量重复+操作时,

1. Stringbuilder 最快，不需要考虑线程同步；
2. StringBuffer次之；
3. String最慢，因为每次都要重新开辟内存，产生很多匿名对象，影响系统性能。

### StringBuilder

`java.lang.StringBuilder`：StringBuilder又称为可变字符序列，它是一个类似于 String 的字符串缓冲区，通过某些方法调用可以改变该序列的长度和内容。

原来`StringBuilder`是个字符串的缓冲区，即它是一个容器，容器中可以装很多字符串。并且能够对其中的字符串进行各种操作。

它的内部拥有一个数组用来存放字符串内容，进行字符串拼接时，直接在数组中加入新内容。StringBuilder会自动维护数组的扩容。原理如下图所示：(默认16字符空间，超过自动扩充)

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-common-classes/01_StringBuilder%E7%9A%84%E5%8E%9F%E7%90%86.bmp)

### 构造方法

常用构造方法有2个：

- `public StringBuilder()`：构造一个空的StringBuilder容器。
- `public StringBuilder(String str)`：构造一个StringBuilder容器，并将字符串添加进去。

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        StringBuilder sb1 = new StringBuilder();
        System.out.println(sb1); // (空白)
        // 使用带参构造
        StringBuilder sb2 = new StringBuilder("jeffery");
        System.out.println(sb2); // jeffery
    }
}
```

### 常用方法

StringBuilder常用的方法有2个：

- `public StringBuilder append(...)`：添加任意类型数据的字符串形式，并返回当前对象自身。
- `public String toString()`：将当前StringBuilder对象转换为String对象。

#### append方法

append方法具有多种重载形式，可以接收任意类型的参数。任何数据作为参数都会将对应的字符串内容添加到StringBuilder中。例如：

```java
public class Demo02StringBuilder {
	public static void main(String[] args) {
		//创建对象
		StringBuilder builder = new StringBuilder();
		//public StringBuilder append(任意类型)
		StringBuilder builder2 = builder.append("hello");
		//对比一下
		System.out.println("builder:"+builder);
		System.out.println("builder2:"+builder2);
		System.out.println(builder == builder2); //true
	    // 可以添加 任何类型
		builder.append("hello");
		builder.append("world");
		builder.append(true);
		builder.append(100);
		// 在开发中，会遇到调用一个方法后，返回一个对象的情况。然后使用返回的对象继续调用方法。
        // 这种时候，我们就可以把代码现在一起，如append方法一样，代码如下
		//链式编程
		builder.append("hello").append("world").append(true).append(100);
		System.out.println("builder:"+builder);
	}
}
```

#### toString方法

通过toString方法，StringBuilder对象将会转换为不可变的String对象。如：

```java
public class Demo16StringBuilder {
    public static void main(String[] args) {
        // 链式创建
        StringBuilder sb = new StringBuilder("Hello").append("World").append("Java");
        // 调用方法
        String str = sb.toString();
        System.out.println(str); // HelloWorldJava
    }
}
```

> StringBuilder已经覆盖重写了Object当中的toString方法。

 









[[java StringUtils]]



