---
Create: 2021年 十月 30日, 星期六 11:07
tags: 
  - Engineering/java
  - 大数据
---
# 线程安全

如果有多个线程在同时运行，而这些线程可能会同时运行这段代码。程序每次运行结果和单线程运行的结果一样，而且其他的变量的值也和预期的是一样的，就是线程安全的。

 通过一个案例，演示线程的安全问题： 电影院要卖票，模拟电影院的卖票过程。本次电影的座位共100个 。 模拟电影院的售票窗口，实现多个窗口同时卖电影票(多个窗口一起卖这100张票)。 需要窗口，采用线程对象来模拟；需要票，采用Runnable接口子类来模拟票：

```java
public class Ticket implements Runnable {
    private int ticket = 100; /* * 执行卖票操作 */ 
    @Override 
    public void run() {
        //每个窗口卖票的操作
        //窗口 永远开启
        while (true) {
            if (ticket > 0) {//有票 可以卖 
                //出票操作 
                //使用sleep模拟一下出票时间 
                try { 
                    Thread.sleep(100); 
                } catch (InterruptedException e) { 
                    // TODO Auto‐generated catch block 
                    e.printStackTrace(); 
                } //获取当前线程对象的名字 
                String name = Thread.currentThread().getName(); 
                System.out.println(name + "正在卖:" + ticket‐‐);
            }
        }
    }
}
```

测试类:

```java
public class Demo {
    public static void main(String[] args) { 
        //创建线程任务对象 
        Ticket ticket = new Ticket(); 
        //创建三个窗口对象 
        Thread t1 = new Thread(ticket, "窗口1"); 
        Thread t2 = new Thread(ticket, "窗口2"); 
        Thread t3 = new Thread(ticket, "窗口3");
        //同时卖票 
        t1.start(); 
        t2.start(); 
        t3.start();
    }
}
```

结果中有一部分这样现象：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-multithreading/image-20200916071652696.png)

发现程序出现了两个问题：

1. 相同的票数,比如5这张票被卖了两回。

2. 不存在的票，比如0票与-1票，是不存在的。

几个窗口(线程)票数不同步了，这种问题称为线程不安全。

> 线程安全问题都是由全局变量及静态变量引起的。若每个线程中对全局变量、静态变量只有读操作，而无写操作，一般来说，这个全局变量是线程安全的；
>
> 若有多个线程同时执行`写操作`，一般都需要考虑线程同步， 否则的话就可能影响线程安全。

[[200 Areas/230 Engineering/231 java|java 目录]]