---
Create: 2021年 十月 30日, 星期六 14:34
tags: 
  - Engineering/java
  - 大数据
---
# 函数式编程简介

`函数式接口`在Java中是指：有且仅有一个抽象方法的接口。

函数式接口，即适用于函数式编程场景的接口。而Java中的函数式编程体现就是Lambda，所以函数式接口就是可以适用于Lambda使用的接口。只有确保接口中有且仅有一个抽象方法，Java中的Lambda才能顺利地进行推导。

> 备注：“语法糖”是指使用更加方便，但是原理不变的代码语法。例如在遍历集合时使用的for-each语法，其实 底层的实现原理仍然是迭代器，这便是“语法糖”。从应用层面来讲，Java中的Lambda可以被当做是匿名内部类的“语法糖”，但是二者在原理上是不同的。

只要确保接口中有且仅有一个抽象方法即可：

```java
修饰符 interface 接口名称 {
    public abstract 返回值类型 方法名称(可选参数信息); 
    // 其他非抽象方法内容
}
```

由于接口当中抽象方法的 public abstract 是可以省略的，所以定义一个函数式接口很简单：

```java
public interface MyFunctionalInterface { 
    void myMethod(); 
}
```

## @FunctionalInterface

与 @Override 注解的作用类似，Java 8中专门为函数式接口引入了一个新的注解： @FunctionalInterface 。该注解可用于一个接口的定义上：

```java
@FunctionalInterface 
public interface MyFunctionalInterface { 
    void myMethod(); 
}
```

一旦使用该注解来定义接口，编译器将会强制`检查`该接口是否确实有且仅有一个抽象方法，否则将会报错。需要注意的是，即使不使用该注解，只要满足函数式接口的定义，这仍然是一个函数式接口，使用起来都一样。

对于刚刚定义好的 MyFunctionalInterface 函数式接口，典型使用场景就是作为方法的参数：

```java
public class Demo09FunctionalInterface {
    // 使用自定义的函数式接口作为方法参数 
    private static void doSomething(MyFunctionalInterface inter) { 
        inter.myMethod(); // 调用自定义的函数式接口方法 
    }
    public static void main(String[] args) { 
        // 调用使用函数式接口的方法 
        doSomething(() ‐> System.out.println("Lambda执行啦！")); 
    }
}
```



在兼顾面向对象特性的基础上，Java语言通过Lambda表达式与方法引用等，为开发者打开了函数式编程的大门。

## Lambda的延迟执行

有些场景的代码执行后，结果不一定会被使用，从而造成性能浪费。而Lambda表达式是延迟执行的，这正好可以作为解决方案，提升性能。

性能浪费的日志案例:一种典型的场景就是对参数进行有条件使用，例如对日志消息进行拼接后，在满足条件的情况下进行打印输出。

```java
public class Demo01Logger {
    private static void log(int level, String msg) { 
        if (level == 1) { 
            System.out.println(msg); 
        } 
    }
    public static void main(String[] args) { 
        String msgA = "Hello"; 
        String msgB = "World"; 
        String msgC = "Java";
        log(1, msgA + msgB + msgC);
    }
}
```

这段代码存在问题：**无论级别是否满足要求**，作为 log 方法的第二个参数，**三个字符串一定会首先被拼接**并传入方法内，然后才会进行级别判断。如果级别不符合要求，那么字符串的拼接操作就白做了，存在性能浪费。

**使用lambda进行优化**：

使用Lambda必然需要一个函数式接口：

```java
@FunctionalInterface 
public interface MessageBuilder { 
    String buildMessage(); 
}
```

然后对 log 方法进行改造：

```java
public class Demo02LoggerLambda {
    private static void log(int level, MessageBuilder builder) { 
        if (level == 1) { 
            System.out.println(builder.buildMessage()); 
        } 
    }
    
    public static void main(String[] args) { 
        String msgA = "Hello"; 
        String msgB = "World"; 
        String msgC = "Java";
        log(1, () ‐> msgA + msgB + msgC );
    }
}
```

这样一来，只有当级别满足要求的时候，才会进行三个字符串的拼接；否则三个字符串将不会进行拼接。

**证明Lambda的延迟**

```java
public class Demo03LoggerDelay {
    private static void log(int level, MessageBuilder builder) { 
        if (level == 1) { 
            System.out.println(builder.buildMessage()); 
        } 
    }
    
    public static void main(String[] args) { 
        String msgA = "Hello"; 
        String msgB = "World"; 
        String msgC = "Java";
        
        log(2, () ‐> { System.out.println("Lambda执行！"); return msgA + msgB + msgC; });
    }
}
```

在不符合级别要求的情况下，Lambda将不会执行。从而达到节省性能的效果。

> 扩展：实际上使用内部类也可以达到同样的效果，只是将代码操作延迟到了另外一个对象当中通过调用方法来完成。而是否调用其所在方法是在条件判断之后才执行的。

## 使用Lambda作为参数和返回值

如果抛开实现原理不说，Java中的Lambda表达式可以被当作是匿名内部类的替代品。如果方法的参数是一个函数 式接口类型，那么就可以使用Lambda表达式进行替代。使用Lambda表达式作为方法参数，其实就是使用函数式接口作为方法参数。

例如 `java.lang.Runnable` 接口就是一个函数式接口，假设有一个 startThread 方法使用该接口作为参数，那么就可以使用Lambda进行传参。这种情况其实和 Thread 类的构造方法参数为 Runnable 没有本质区别。

```java
public class Demo04Runnable {
    private static void startThread(Runnable task) { 
        new Thread(task).start(); 
    }
    
    public static void main(String[] args) { 
        startThread(() ‐> System.out.println("线程任务执行！")); 
    }
}
```

类似地，如果一个方法的返回值类型是一个函数式接口，那么就可以直接返回一个Lambda表达式。当需要通过一个方法来获取一个 `java.util.Comparator` 接口类型的对象作为排序器时,就可以调该方法获取。

```java
import java.util.Arrays; import java.util.Comparator;

public class Demo06Comparator {
    private static Comparator<String> newComparator() { 
        return (a, b) ‐> b.length() ‐ a.length(); 
    }
    
    public static void main(String[] args) { 
        String[] array = { "abc", "ab", "abcd" };
        System.out.println(Arrays.toString(array)); 
        Arrays.sort(array, newComparator()); 
        System.out.println(Arrays.toString(array)); 
    }
}
```



[[200 Areas/230 Engineering/232 大数据/java|java 目录]]