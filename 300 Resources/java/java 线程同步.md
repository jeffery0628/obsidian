---
Create: 2021年 十月 30日, 星期六 11:09
tags: 
  - Engineering/java
  - 大数据
---
# 线程同步

当我们使用多个线程访问同一资源的时候，且多个线程中对资源有`写操作`，就容易出现线程安全问题。 要解决上述多线程并发访问一个资源的安全性问题:也就是解决重复票与不存在票问题，Java中提供了同步机制 (`synchronized`)来解决。

> 根据案例简述：
>
> 窗口1线程进入操作的时候，窗口2和窗口3线程只能在外等着，窗口1操作结束，窗口1和窗口2和窗口3才有机会进入代码去执行。也就是说在某个线程修改共享资源的时候，其他线程不能修改该资源，等待修改完毕同步之后，才能去抢夺CPU 资源，完成对应的操作，保证了数据的同步性，解决了线程不安全的现象。

为了保证每个线程都能正常执行原子操作,Java引入了线程同步机制。有三种方式完成同步操作：

1. 同步代码块。
2. 同步方法。
3. 锁机制。

## 同步代码块

**同步代码块**： synchronized 关键字可以用于方法中的某个区块中，表示只对这个区块的资源实行互斥访问。

```java
synchronized(同步锁){
    需要同步操作的代码 
}
```

```java
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private  int ticket = 100;

    //创建一个锁对象
    Object obj = new Object();

    //设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
           //同步代码块
            synchronized (obj){
                //先判断票是否存在
                if(ticket>0){
                    //提高安全问题出现的概率,让程序睡眠
                    try {
                        Thread.sleep(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }

                    //票存在,卖票 ticket--
                    System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                    ticket--;
                }
            }
        }
    }
}
```

> 1. 通过代码块中的锁对象,可以使用任意的对象
> 2. 但是必须保证多个线程使用的锁对象是同一个
> 3. 锁对象作用:把同步代码块锁住,只让一个线程在同步代码块中执行

## 同步锁

对象的同步锁只是一个概念,可以想象为在对象上标记了一个锁.

1. 锁对象可以是任意类型。

2. 多个线程对象要使用同一把锁。

> 注意:在任何时候,最多允许一个线程拥有同步锁,谁拿到锁就进入代码块,其他的线程只能在外等着 (BLOCKED)。

```java
public class Ticket implements Runnable{ 
    private int ticket = 100;
    Object lock = new Object(); /* * 执行卖票操作 */ 
    @Override 
    public void run() {//每个窗口卖票的操作
        //窗口 永远开启
        while(true){
            synchronized (lock) { 
                if(ticket>0){//有票 可以卖 
                    //出票操作 
                    //使用sleep模拟一下出票时间 
                    try { 
                        Thread.sleep(50); 
                    } catch (InterruptedException e) { 
                        // TODO Auto‐generated catch block 
                        e.printStackTrace(); 
                    } 
                    //获取当前线程对象的名字 
                    String name = Thread.currentThread().getName();
                    System.out.println(name+"正在卖:"+ticket‐‐);
                }
            }
        }
    }
}
```

当使用了同步代码块后，上述的线程的安全问题，解决了。

## 同步方法

**同步方法**:使用synchronized修饰的方法,就叫做同步方法,保证A线程执行该方法的时候,其他线程只能在方法外等着。

**使用步骤**:

1. 把访问了共享数据的代码抽取出来,放到一个方法中
2. 在方法上添加synchronized修饰符

```java
public synchronized void method(){ 
    可能会产生线程安全问题的代码 
}
```

```java
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private static int ticket = 100;


    //设置线程任务:卖票
    @Override
    public void run() {
        System.out.println("this:"+this);//this:com.jeffery.demo08.Synchronized.RunnableImpl@58ceff1
        //使用死循环,让卖票操作重复执行
        while(true){
            payTicketStatic();
        }
    }

    public static /*synchronized*/ void payTicketStatic(){
        synchronized (RunnableImpl.class){
            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }
        }

    }

    public /*synchronized*/ void payTicket(){
        synchronized (this){
            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

                //票存在,卖票 ticket--
                System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                ticket--;
            }
        }

    }
}

```

> `同步方法`的锁对象是实现类对象`new RunnableImpl()`，也是就是this。
>
> `静态同步方法`的锁对象不能是this，this是创建对象之后产生的,静态方法优先于对象产生，因此静态方法的锁对象是本类的class属性-->class文件对象(反射)。

## Lock锁

`java.util.concurrent.locks.Lock `机制提供了比`synchronized`代码块和`synchronized`方法更广泛的锁定操作, 同步代码块/同步方法具有的功能Lock都有,除此之外更强大,更体现面向对象。

Lock锁也称同步锁，Lock锁接口中的方法：

1. public void lock() :加同步锁。 
2. public void unlock() :释放同步锁。

使用步骤:

1. 在成员位置创建一个ReentrantLock对象
2. 在可能会出现安全问题的代码前调用Lock接口中的方法lock获取锁
3. 在可能会出现安全问题的代码后调用Lock接口中的方法unlock释放锁

```java
public class RunnableImpl implements Runnable{
    //定义一个多个线程共享的票源
    private  int ticket = 100;

    //1.在成员位置创建一个ReentrantLock对象
    Lock l = new ReentrantLock();

    //设置线程任务:卖票
    @Override
    public void run() {
        //使用死循环,让卖票操作重复执行
        while(true){
            //2.在可能会出现安全问题的代码前调用Lock接口中的方法lock获取锁
            l.lock();

            //先判断票是否存在
            if(ticket>0){
                //提高安全问题出现的概率,让程序睡眠
                try {
                    Thread.sleep(10);
                    //票存在,卖票 ticket--
                    System.out.println(Thread.currentThread().getName()+"-->正在卖第"+ticket+"张票");
                    ticket--;
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    //3.在可能会出现安全问题的代码后调用Lock接口中的方法unlock释放锁
                    l.unlock();//无论程序是否异常,都会把锁释放
                }
            }
        }
    }
}
```



[[200 Areas/230 Engineering/232 大数据/java|java 目录]]