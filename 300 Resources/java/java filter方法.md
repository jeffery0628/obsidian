---
Create: 2021年 十月 30日, 星期六 14:46
tags: 
  - Engineering/java
  - 大数据
---
## ﬁlter

以通过 filter 方法将一个流转换成另一个子集流。该接口接收一个 Predicate 函数式接口参数（可以是一个Lambda或方法引用）作为筛选条件。

```java
Stream<T> filter(Predicate<? super T> predicate);
```

> `java.util.stream.Predicate` 函数式接口，其中唯一的抽象方法为：`boolean test(T t);`该方法将会产生一个`boolean`值结果，代表指定的条件是否满足。如果结果为true，那么Stream流的 filter 方法将会留用元素；如果结果为false，那么 filter 方法将会舍弃元素。

```java
import java.util.stream.Stream;

public class Demo07StreamFilter { 
    public static void main(String[] args) { 
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若"); 
        Stream<String> result = original.filter(s ‐> s.startsWith("张")); 
    } 
}
```

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]