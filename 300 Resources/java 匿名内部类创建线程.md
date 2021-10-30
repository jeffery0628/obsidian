---
Create: 2021年 十月 30日, 星期六 11:05
tags: 
  - Engineering/java
  - 大数据
---
# 匿名内部类创建线程

使用线程的匿名内部类方式，可以方便的实现每个线程执行不同的线程任务操作。使用匿名内部类的方式实现Runnable接口，重新Runnable接口中的run方法：

```java
public class NoNameInnerClassThread { 
    public static void main(String[] args) { 
        //new Runnable(){ 
        //    public void run(){ 
        //        for (int i = 0; i < 20; i++) { 
        //           System.out.println("张宇:"+i); 
        //        } 
        //    } 
        //}; //‐‐‐这个整体 相当于new MyRunnable()
        Runnable r = new Runnable(){ 
            public void run(){ 
                for (int i = 0; i < 20; i++) { 
                    System.out.println("张宇:"+i); 
                } 
            }
        };
        
        new Thread(r).start();
        for (int i = 0; i < 20; i++) { 
            System.out.println("费玉清:"+i); 
        }
    }
}
```

