---
Create: 2021年 十月 30日, 星期六 11:48
tags: 
  - Engineering/java
  - 大数据
---
# 类与对象

## 概念

### 类的概念

类是对现实世界事物的抽象定义, 这个抽象定义就可以基本把某事物描述清楚. 要想描述清楚事物, 必须要知道事物有哪些特征(数据, 用变量保存), 有哪些行为(用方法描述), 当某事物的特征和行为都描述清楚后, 我们就认为对这个事物有一个大概的把握。

### 对象的概念

​	对象就是一个类的实实在在的实体, 也称为实例, 所以对象(object)也称为实例(instance)。

### 类与对象之间的关系

类是描述事物的, 一旦描述清楚, 就可以代表一类事物了, 但是类只是概念, 要想使用实体, 必须要有对象, 但是从时间的先后顺序来讲, 是先有类, 才有的对象, 因为类就像是一个模板, 而对象就像是用这个模板制造出来的产品。

## 类

### 类的语法格式

```java
修饰符 class 类名{
    属性声明;
    方法声明;
}
```



### 属性的语法格式

```java
修饰符 类型 属性名=初值;
```

> 说明
>
> 修饰符private:该属性只能由该类的方法访问。
>
> 修饰符public:该属性可以被该类以外的方法访问。   
>
> 类型：任何基本类型，如int、boolean或任何引用类型。

#### 属性的使用

属性通常是要隶属于某个对象来使用的, 要想使用属性, 必须要先创建对象, 创建对象的语法很简单

```java
类名 引用变量名 = new 类名();
引用变量名.name = "JAVA";
System.out.println(引用变量名.name); 

如：
public class TeacherTest {
	
	public static void main(String[] args) { 
		Teacher t = new Teacher();
		t.name = "张三"; // 把”张三”值赋给t对象的属性name
		System.out.println(t.name); // 打印t对象的name属性值, 输出就是”张三”
}
}
```

属性的使用和普通变量没有区别, 唯一的区别就在于属性是隶属于某个对象了。

### 构造器

对象在刚创建时进行的工作就称为初始化, 初始化的主要工作是针对对象的属性的。当对象创建时, 希望对象的属性值被正确赋值, 此时就需要用到了构造器, 构造器也称为构造方法, 本质上构造器就是一个方法, 是一个特殊的方法。

```java
修饰符 类名(参数类型1 形参1, 参数类型2 形参2….) {
	代码块;
}

如：
class Teacher {

    private String name;
    private int age;
    private String gender ;

    public Teacher() { // 无参构造器
        name = "张三";
        age = 35;
        gender = "男";
    }
    public  Teacher(String name,int age,String gender){ //含参构造器
        this.name = name;
        this.age = age;
        this.gender = gender;
    }
}
```

> 使用this的优点
>
> 1.	使用this可以提高代码的可读性, 强调要使用的是当前对象.
>
> 2.	在方法中, 如果局部变量和属性重名, 必须使用this, 用以区分属性和局部变量, 并且这样局部变量的含义也更清晰.
>
> 3.	this(…)调用可以简化构造器调用, 并有利于维护. 如果有修改的需要, 只要修改被调用的构造器就可以了.





### 方法

#### 方法概念

方法是类或对象行为特征的抽象，也称为函数，Java里的方法不能独立存在，所有的方法必须定义在类里。方法也可以描述为是某个功能的执行体, 一个方法通常对应一个功能。

#### 方法的声明

```java
修饰符 返回值类型 方法名（参数类型 形参1，参数类型 形参2，….）｛
  	程序代码
  	return 返回值;
｝
```

> 其中：
>
> 形式参数：在方法被调用时用于接收外部传入的数据的变量。
>
> 参数类型：就是该形式参数的数据类型。
>
> 返回值：方法在执行完毕后返还给调用它的程序的数据。
>
> 返回值类型：方法要返回的结果的数据类型。
>
> 实际参数：调用方法时实际传给函数形式参数的数据。

> 注：
>
> 一个类中可以有多个方法。
>
> 方法中只能调用方法，不可以在方法内部定义方法。
>
> 方法声明不是方法调用。

```java
public class Person {
    
    public int add(int a, int b) {
        return a + b;
    }

    public void test() {
        System.out.println("test()");
        return;
    }
	public static void main(String[] args) { 
		Person t = new Person();
		t.test(); // 当执行程序时, 打印输出”test()”
	}
}
```



### 重载

在同一个类中，允许存在一个以上的同名方法，只要它们的**参数不同**即可。

> 参数不同的含义是：仅 参数个数不同 或者 类型不同 或者 顺序不同.



```java
public class Person {
    
    // 这个方法就可以和下面2个方法形成重载
    public int add(int a, int b) {
        System.out.println(“a + b”);
        int c = a + b;
        return c;
    }

    public double add(int a, double b) {
        return a + b;
    }

    public double add(double a, int b) {
        return a + b;
    }

    public void test() {
        System.out.println("test()");
    }
	
	public static void main(String[] args) { 
		Person t = new Person();
        System.out.println(t.add(30, 50)); // 调用 int add(int a, int b)
		System.out.println(t.add(209, 0.502)); // 调用 double add(int a, double b)
	}
}
```

> 在调用同名方法时, 只需要实参不同即可, 调用者调用这个方法就变得简单, 也不用再记忆多个不同的方法名。

#### 参数传递的机制

传参的本质是方法在调用时, 把实参的值赋值给形参(形参是局部变量), 也称为传值调用。



### 可变参数

当一个方法中的参数类型都相同, 但是个数不确定的情况下，可使用可变参数。

```java
public class VarArgs {
	
	/*类中的方法的功能类似, 但是参数个数不确定
	public int avg(int a, int b) {
		return (a + b) / 2;
	}
	
	public int avg(int a, int b, int c) {
		return (a + b + c) / 3;
	}
	
	public int avg(int a, int b, int c, int d) {
		return (a + b + c + d) / 4;
	}*/
	
	// 可变参数, 参数的个数可以是任意个, 只能放在参数列表的最后
	public int avg(String a, int... values) { // int...是数组, 同时又能兼容任意个数参数
		int sum = 0;
		for (int i = 0; i < values.length; i++) {
			sum += values[i];
		}
		return sum / values.length;
	}
}
```

> 可变参数的本质上是方法在调用时, 实际传递的是数组.

### javabean

JavaBean是一种Java语言写成的可重用组件。 所谓javaBean，是指符合如下标准的Java类：

1. 类是公共的
2. 有一个无参的公共的构造器
3. 有属性，且有对应的get、set方法
4. 用户可以使用JavaBean将功能、处理、值、数据库访问和其他任何可以用java代码创造的对象进行打包，并且其他的开发者可以通过内部的JSP页面、Servlet、其他JavaBean、applet程序或者应用来使用这些对象。用户可以认为JavaBean提供了一种随时随地的复制和粘贴的功能，而不用关心任何改变。

```java
public class TestJavaBean{
	private String name;  //属性一般定义为private
	private int age;
	public  TestJavaBean(){}
	public int getAge(){
	     return age;
	}
	public void setAge(int age){
	     this.age = age;
	}
	public String getName(){
	    return name;
	}
	public void setName(String name){
	    this.name = name;
	}
}
```

## 对象

1. 类一旦写好了, 就可以使用关键字new创建对象；
2. 当一个对象被创建时, 这个对象就会包含类中所有的属性值；
3. 对象之间是独立的；
4. 同一个类的不同对象虽然是独立的, 但是它们所占用的内存空间大小是一样的.

### 匿名对象

在创建对象后并不把对象的地址保存在引用变量中, 而是直接使用创建好的对象的引用访问成员。

```java
public class TeacherTest {
	public static void main(String[] args) { 
		new Teacher().eat(“宫暴鸡丁”);  // 后面不能再使用这个对象了
	}
}

```

> 因为对象没有使用引用变量保存, 所以对象访问完成后, 就无法再次访问了.
>
> 使用场景：
>
> 1. 适用于对象的一次性使用场景中
> 2. 适用于方法调用时传递对象
> 3. 适用于对象的传递(对象作为参数传递)





[[200 Areas/230 Engineering/231 java|java 目录]]