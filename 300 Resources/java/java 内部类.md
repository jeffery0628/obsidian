---
Create: 2021年 十月 30日, 星期六 11:58
tags: 
  - Engineering/java
  - 大数据
---
# 内部类

在Java中，允许一个类的定义位于另一个类的内部，前者称为内部类，后者称为外部类。Inner class一般用在定义它的类或语句块之内，在外部引用它时必须给出完整的名称。Inner class的名字不能与包含它的类名相同；

Inner class可以使用外部类的私有数据，因为它是外部类的成员，同一个类的成员之间可相互访问。而外部类要访问内部类中的成员需要通过：内部类.成员  或者  内部类对象.成员  的方式。

## 成员内部类

普通内部类：

```java
class A {
	private int s;
	public class B {
		public void mb() {
			s = 100; // 在普通内部类的方法中, 可以直接外部类的私有成员.
			System.out.println("在内部类B中s=" + s);
		}  
	}
	public void func() {
		B i = new B();
		i.mb();
	} 
}
```

嵌套类：被static修饰的成员内部类就称为嵌套类

```java
class A {
	private int s;
	public static class B {
		public void mb() {
			//s = 100; // 在嵌套类的方法中, 不可以直接访问外部类的非静态成员.
			//System.out.println("在内部类B中s=" + s);
		}  
	}
	public void func() {
		B i = new B();
		i.mb();
	} 
}
```

## 局部内部类

在方法体中声明的内部类就是局部内部类, 局部内部类的范围和局部变量类似。

```java
public class Test {    
	public static void main(String args[]){
		class A {
			public void test(){};
		}; 
        
		A a = new A();
		a.test();
    }
}
```

## 匿名内部类

在方法中声明的内部类, 但是没有class关键字和具体类名, 称为匿名内部类, 因为没有类名, 所以必须在声明内部类的同时创建对象, 否则无法创建对象了。所以匿名内部类的语法是 :

```java
父类 引用 = new 父类(实参列表) {类体};
```

匿名内部类最常用用法是new 后面的类名是已经存在的类, 或抽象类, 或接口. 如果是抽象类或接口, 则匿名内部类类体中必须实现全部的抽象方法, 由此可见, 匿名内部类只能作为new后面的类或抽象或接口的子类存在, 但是没有类名, 所以通常在声明的时候就创建对象.

```java
public interface A {
    public void a();
}

public class Test {    
	public static void main(String args[]){
		new A() {
            @Override
			public void a() {
                System.out.println(“匿名内部类实现接口方法”);
            }
		}.a(); // 打印输出内容…
        
    }
}
```





[[200 Areas/230 Engineering/232 大数据/java|java 目录]]