---
Create: 2021年 十月 30日, 星期六 14:45
tags: 
  - Engineering/java
  - 大数据
---
# 获取 stream 流

`java.util.stream.Stream<T> `是Java 8新加入的最常用的流接口。（并不是一个函数式接口。）获取一个流非常简单，有以下几种常用的方式：

- 所有的 `Collection` 集合都可以通过 `stream` 默认方法获取流； 
- `Stream` 接口的静态方法 `of` 可以获取数组对应的流。

## Collection获取stream流

`java.util.Collection` 接口中加入了default方法 `stream` 用来获取流，所以其所有实现类均可获取流。

```java
import java.util.*; 
import java.util.stream.Stream;

public class Demo04GetStream {
    public static void main(String[] args) { 
        List<String> list = new ArrayList<>(); 
        // ...
        Stream<String> stream1 = list.stream();
        
        Set<String> set = new HashSet<>(); 
        // ...
        Stream<String> stream2 = set.stream();
        
        Vector<String> vector = new Vector<>();
        // ...Stream<String> stream3 = vector.stream();
    }
}
```

## Map获取流

`java.util.Map` 接口不是 `Collection` 的子接口，且其`K-V`数据结构不符合流元素的单一特征，所以获取对应的流 需要分`key`、`value`或`entry`等情况：

```java
import java.util.HashMap; 
import java.util.Map; 
import java.util.stream.Stream;

public class Demo05GetStream {
    public static void main(String[] args) { 
        Map<String, String> map = new HashMap<>(); 
        // ...
        
        Stream<String> keyStream = map.keySet().stream(); 
        Stream<String> valueStream = map.values().stream(); 
        Stream<Map.Entry<String, String>> entryStream = map.entrySet().stream();
    }
}
```

## 数组获取流

如果使用的不是集合或映射而是数组，由于数组对象不可能添加默认方法，所以 Stream 接口中提供了静态方法 of ，使用很简单：

```java
import java.util.stream.Stream;
public class Demo06GetStream { 
    public static void main(String[] args) { 
        String[] array = { "张无忌", "张翠山", "张三丰", "张一元" }; 
        Stream<String> stream = Stream.of(array); 
    } 
}
```

> `of` 方法的参数其实是一个可变参数，所以支持数组。


