---
Create: 2021年 十月 25日, 星期一 13:40
Linked Areas: 
Linked Project:
Linked Tools: 
Other  Links: 
tags: 
  - Engineering/java
  - 大数据
---



# 数组定义

数组就是存储数据长度固定的容器，保证多个数据的数据类型要一致。

特点：

1. 数组是一种引用数据类型
2. 数组当中的多个数据，类型必须统一
3. 数组的长度在程序运行期间不可改变

# 创建数组

两种常见的初始化方式：

1. 动态初始化：在创建数组的时候，直接指定数组当中的数据元素个数。
2. 静态初始化：在创建数组的时候，不直接指定数据个数多少，而是直接将具体的数据内容进行指定。

> 虽然静态初始化没有直接告诉长度，但是根据大括号里面的元素具体内容，也可以自动推算出来长度。

## 动态初始化

```java
//数组存储的数据类型[] 数组名字 = new 数组存储的数据类型[长度];
int[] arr = new int[3];
```


- [] : 表示数组。 
- 数组名字：为定义的数组起个变量名，满足标识符规范，可以使用名字操作数组。 
- new：关键字，创建数组使用的关键字。 
- 数组存储的数据类型： 创建的数组容器可以存储什么数据类型。 
- [长度]：数组的长度，表示数组容器中可以存储多少个元素。 

> 注意：数组有定长特性，长度一旦指定，不可更改。 和水杯道理相同，买了一个2升的水杯，总容量就是2升，不能多也不能少。
>
> 使用动态初始化数组的时候，其中的元素将会自动拥有一个默认值。规则如下：
>
> - 如果是整数类型，那么默认为0；
> - 如果是浮点类型，那么默认为0.0；
> - 如果是字符类型，那么默认为'\u0000'；
> - 如果是布尔类型，那么默认为false；
> - 如果是引用类型，那么默认为null。

## 静态初始化

```java
//数据类型[] 数组名 = new 数据类型[]{元素1,元素2,元素3...};
int[] arr = new int[]{1,2,3,4,5};
//数据类型[] 数组名 = {元素1,元素2,元素3...};
int[] arr = {1,2,3,4,5};
```

> 静态初始化其实也有默认值的过程，只不过系统自动马上将默认值替换成为了大括号当中的具体数值。

# 数组的访问

`索引`： 每一个存储到数组的元素，都会自动的拥有一个编号，从0开始，这个自动编号称为数组索引 (index)，可以通过数组的索引访问到数组中的元素。

`数组的长度属性`： 每个数组都具有长度，而且是固定的，Java中赋予了数组的一个属性，可以获取到数组的 长度，语句为： 数组名.length ，属性length的执行结果是数组的长度，int类型结果。数组的最大索引值为数组名.length-1 。

# 数组内存图

## 内存

内存是计算机中的重要原件，临时存储区域，作用是运行程序。我们编写的程序是存放在硬盘中的，在硬盘中的程序是不会运行的，必须放进内存中才能运行，运行完毕后会清空内存。 Java虚拟机要运行程序，必须要对内存进行空间的分配和管理。

| 区域名称   | 作用                                                       |
| ---------- | ---------------------------------------------------------- |
| 寄存器     | 给CPU使用，和开发无关。                                    |
| 本地方法栈 | JVM在使用操作系统功能的时候使用，和开发无关。              |
| `方法区`   | 存储可以运行的class文件。                                  |
| `堆内存`   | 存储对象或者数组，new来创建的，都存储在堆内存。            |
| `方法栈`   | 方法运行时使用的内存，比如main方法运行，进入方法栈中执行。 |

## 数组在内存中的存储

> 直接打印数组名称，得到的是数组对应的：内存地址哈希值。

```java
public static void main(String[] args) { 
	int[] arr = new int[3]; 
	int[] arr2 = new int[2]; 
	System.out.println(arr); 
    System.out.println(arr2); 
}
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-array/image-20200906110809269.png)

```java
public static void main(String[] args) {

	// 定义数组，存储3个元素 
	int[] arr = new int[3]; 
	//数组索引进行赋值 
	arr[0] = 5; 
	arr[1] = 6; 
	arr[2] = 7; 
	//输出3个索引上的元素值 
	System.out.println(arr[0]); 
	System.out.println(arr[1]); 
	System.out.println(arr[2]); 
	//定义数组变量arr2，将arr的地址赋值给arr2 
	int[] arr2 = arr; arr2[1] = 9; 
	System.out.println(arr[1]);

}
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-array/image-20200906110927287.png)

# 数组常见操作

## 下标越界

不能访问数组中不存在的索引，程序运 行后，将会抛出 `ArrayIndexOutOfBoundsException` 数组越界异常。

## 数组空指针异常

数组变量不可赋null，否则运行的时候会抛出 NullPointerException 空指针异常。

## 遍历

```java
for (int i = 0; i < arr.length; i++) { 
	System.out.println(arr[i]); 
}
```

## 数组作为方法参数和返回值

数组作为方法`参数传递`，传递的参数是数组`内存地址`。

```
public static void main(String[] args) { 
	int[] arr = { 1, 3, 5, 7, 9 }; 
	//调用方法，传递数组 
	printArray(arr); 
} 
/* 创建方法，方法接收数组类型的参数 进行数组的遍历 */ 
public static void printArray(int[] arr) { 
	for (int i = 0; i < arr.length; i++) { 
    	System.out.println(arr[i]); 
    } 
}
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-array/image-20200906111629619.png)



数组作为方法的`返回值`，返回的是数组的`内存地址`.

```java
public static void main(String[] args) {

	//调用方法，接收数组的返回值 
	//接收到的是数组的内存地址 
	int[] arr = getArray(); 
	for (int i = 0; i < arr.length; i++) {
    	System.out.println(arr[i]); 
    }

}

/*
创建方法，返回值是数组类型
return返回数组的地址
*/ 
public static int[] getArray() {
    int[] arr = { 1, 3, 5, 7, 9 };
    //返回数组的地址，返回到调用者
    return arr; 
}
```

> 注：
>
> 方法的参数为基本类型时,传递的是数据值. 方法的参数为引用类型时,传递的是地址值.


[[200 Areas/230 Engineering/232 大数据/java|java 目录]]