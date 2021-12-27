---
Create: 2021年 十月 29日, 星期五 13:36
tags: 
  - Engineering/java
  - 大数据
---
# 创建线程类

创建线程的方式总共有两种：

1. 一种是继承Thread类方式
2. 一种是实现Runnable接口方式

## 继承Thread 类

Java使用`java.lang.Thread`类代表**线程**，所有的线程对象都必须是Thread类或其子类的实例。每个线程的作用是完成一定的任务，实际上就是一段顺序执行的代码。Java使用线程执行体来代表这段程序流。Java中通过继承Thread类来**创建**并**启动多线程**的步骤如下：

1. 定义Thread类的子类，并重写该类的`run()`方法，该`run()`方法的方法体就代表了线程需要完成的任务,因此把`run()`方法称为线程执行体。
2. 创建Thread子类的实例，即创建了线程对象
3. 调用线程对象的start()方法来启动该线程

代码如下：

自定义线程类：

~~~java
public class MyThread extends Thread {
	//定义指定线程名称的构造方法
	public MyThread(String name) {
		//调用父类的String参数的构造方法，指定线程的名称
		super(name);
	}
	/**
	 * 重写run方法，完成该线程执行的逻辑
	 */
	@Override
	public void run() {
		for (int i = 0; i < 10; i++) {
			System.out.println(getName()+"：正在执行！"+i);
		}
	}
}
~~~

测试类：

```java
public class Demo01 {
	public static void main(String[] args) {
		//创建自定义线程对象
		MyThread mt = new MyThread("新的线程！");
		//开启新线程
		mt.start();
		//在主方法中执行for循环
		for (int i = 0; i < 10; i++) {
			System.out.println("main线程！"+i);
		}
	}
}
```

## 实现 Runnable 接口

采用 `java.lang.Runnable `也是非常常见的一种，只需要重写`run()`方法即可。

步骤如下：

1. 定义Runnable接口的实现类，并重写该接口的run()方法，该run()方法的方法体同样是该线程的线程执行体。
2. 创建Runnable实现类的实例，并以此实例作为Thread类的`构造函数`的参数`target`来创建Thread对象，该Thread对象才是真正的线程对象。
3. 调用线程对象的`start()`方法来启动线程。

> 1. 要实际实现多线程，总会创建Thread对象。

```java
public class MyRunnable implements Runnable{
    @Override 
    public void run() {
        for (int i = 0; i < 20; i++) {
            System.out.println(Thread.currentThread().getName()+" "+i);
        } 
    }
}
```

```java
public class Demo {
    public static void main(String[] args) {
        //创建自定义类对象 线程任务对象
        MyRunnable mr = new MyRunnable(); 
        //创建线程对象 
        Thread t = new Thread(mr, "小强"); 
        t.start(); 
        for (int i = 0; i < 20; i++) { 
            System.out.println("旺财 " + i); 
        }
    }
}
```

通过实现Runnable接口，使得该类有了多线程类的特征。`run()`方法是多线程程序的一个执行目标。所有的多线程代码都在run方法里面。Thread类实际上也是实现了Runnable接口的类：`public class Thread implements Runnable `.

在启动的多线程的时候，需要先通过Thread类的构造方法Thread(Runnable target) 构造出对象，然后调用Thread 对象的start()方法来运行多线程代码。



> 1. 实际上所有的多线程代码都是通过运行Thread的`start()`方法来运行的。因此，不管是继承Thread类还是实现 Runnable接口来实现多线程，最终还是通过Thread的对象的API来控制线程的，熟悉Thread类的API是进行多线程编程的基础。
> 2. Runnable对象仅仅作为Thread对象的target，Runnable实现类里包含的run()方法仅作为线程执行体。 而实际的线程对象依然是Thread实例，只是该Thread线程的start方法负责执行其target的run()方法。

## Thread和Runnable的区别

如果一个类继承Thread，则不适合资源共享。但是如果实现了Runable接口的话，则很容易的实现资源共享。

总结： 实现Runnable接口比继承Thread类所具有的优势：

1. 适合多个相同的程序代码的线程去`共享同一个资源`。

2. 可以避免java中的`单继承的局限性`。

3. 增加程序的健壮性，实现`解耦`操作，代码可以被多个线程共享，代码和线程独立。

4. 线程池只能放入实现Runable或Callable类线程，不能直接放入继承Thread的类。

> 扩充：在java中，每次程序运行至少启动2个线程。一个是main线程，一个是垃圾收集线程。因为每当使用 java命令执行一个类的时候，实际上都会启动一个JVM，每一个JVM其实在就是在操作系统中启动了一个进程。



[[200 Areas/230 Engineering/232 大数据/java|java 目录]]