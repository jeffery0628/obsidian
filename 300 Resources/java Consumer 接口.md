---
Create: 2021年 十月 30日, 星期六 14:37
tags: 
  - Engineering/java
  - 大数据
---



## Consumer接口

`java.util.function.Consumer<T>` 接口则正好与`Supplier`接口相反，它不是生产一个数据，而是消费一个数据， 其数据类型由泛型决定。

### 抽象方法：accept

Consumer 接口中包含抽象方法 void accept(T t) ，意为消费一个指定泛型的数据。

```java
import java.util.function.Consumer;
public class Demo09Consumer {
    private static void consumeString(Consumer<String> function) { 
        function.accept("Hello"); 
    }
    
    public static void main(String[] args) { 
        consumeString(s -> System.out.println(s)); 
    }
}
```

### 默认方法：andThen

如果一个方法的参数和返回值全都是 `Consumer` 类型，那么就可以实现效果：消费数据的时候，首先做一个操作， 然后再做一个操作，实现组合。而这个方法就是 Consumer 接口中的default方法 andThen 。下面是JDK的源代码：

```java
default Consumer<T> andThen(Consumer<? super T> after) { 
    Objects.requireNonNull(after); 
    return (T t) ‐> { accept(t); after.accept(t); }; 
}
```

> `java.util.Objects `的 `requireNonNull` 静态方法将会在参数为null时主动抛出 `NullPointerException `异常。这省去了重复编写if语句和抛出空指针异常的麻烦。

要想实现组合，需要两个或多个Lambda表达式即可，而 andThen 的语义正是“一步接一步”操作。例如两个步骤组合的情况：

```java
import java.util.function.Consumer;

public class Demo10ConsumerAndThen {
    private static void consumeString(Consumer<String> one, Consumer<String> two) {
        one.andThen(two).accept("Hello"); 
    }
    
    public static void main(String[] args) { 
        consumeString( s ‐> System.out.println(s.toUpperCase()), s ‐>System.out.println(s.toLowerCase())); 
    }
}
```

运行结果将会首先打印完全大写的HELLO，然后打印完全小写的hello。当然，通过链式写法可以实现更多步骤的组合。

#### 格式化打印信息

下面的字符串数组当中存有多条信息，按照格式 `姓名：XX。性别：XX`。 的格式将信息打印出来。打印姓名的动作作为第一个 Consumer 接口的Lambda实例，打印性别的动作作为第二个 Consumer 接口的Lambda实 例，将两个 Consumer 接口按照顺序“拼接”到一起。

```java
import java.util.function.Consumer;

public class DemoConsumer {
    public static void main(String[] args) { 
        String[] array = { "迪丽热巴,女", "古力娜扎,女", "马尔扎哈,男" }; 
        printInfo(s ‐> System.out.print("姓名：" + s.split(",")[0]), s ‐> System.out.println("。性别：" + s.split(",")[1] + "。"), array); 
    }
    
    private static void printInfo(Consumer<String> one, Consumer<String> two, String[] array) { 
        for (String info : array) { 
            one.andThen(two).accept(info); // 姓名：迪丽热巴。性别：女。 
        } 
    }

}
```

## 

