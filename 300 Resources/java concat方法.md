---
Create: 2021年 十月 30日, 星期六 14:55
tags: 
  - 

---
## concat

如果有两个流，希望合并成为一个流，那么可以使用 Stream 接口的静态方法 concat .

```java
static <T> Stream<T> concat(Stream<? extends T> a, Stream<? extends T> b)
```

> 这是一个静态方法，与 java.lang.String 当中的 concat 方法是不同的。

```java
import java.util.stream.Stream;

public class Demo12StreamConcat {
    public static void main(String[] args) { 
        Stream<String> streamA = Stream.of("张无忌"); 
        Stream<String> streamB = Stream.of("张翠山"); 
        Stream<String> result = Stream.concat(streamA, streamB); 
        System.out.println(result.count()); // 2
    }
}
```
