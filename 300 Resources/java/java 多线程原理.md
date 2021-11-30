---
Create: 2021年 十月 29日, 星期五 13:40
tags: 
  - Engineering/java
  - 大数据
---





# 多线程原理

自定义线程类：

```java
public class MyThread extends Thread{ 
    /* 
    * 利用继承中的特点 
    * 将线程名称传递 进行设置 
    */ 
    public MyThread(String name){ 
        super(name); 
    } 
    /*
    * 重写run方法 
    * 定义线程要执行的代码 
    */ 
    public void run(){
        for (int i = 0; i < 20; i++) {
            //getName()方法 来自父亲 
            System.out.println(getName()+i);
        }
    }
}
```

测试类：

```java
public class Demo {
    public static void main(String[] args) { 
        System.out.println("这里是main线程"); 
        MyThread mt = new MyThread("小强"); 
        mt.start();//开启了一个新的线程 
        for (int i = 0; i < 20; i++) { 
            System.out.println("旺财:"+i); 
        }
    }
}
```

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-multithreading/image-20200915232346889.png)

程序启动运行main时候，java虚拟机启动一个进程，主线程main在main()调用时候被创建。随着调用mt的对象的 `start()`方法，另外一个新的线程也启动了，这样，整个应用就在多线程下运行。

通过这张图可以很清晰的看到多线程的执行流程，那么为什么可以完成并发执行呢？

多线程执行时，在栈内存中，其实每一个执行线程都有一片自己所属的栈内存空间。进行方法的压栈和弹栈。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-multithreading/image-20200915232808625.png)

当执行线程的任务结束了，线程自动在栈内存中释放了。但是当所有的执行线程都结束了，那么进程就结束了。

# 
