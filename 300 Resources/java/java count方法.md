---
Create: 2021年 十月 30日, 星期六 14:53
tags: 
  - 

---
## count

正如旧集合 Collection 当中的 size 方法一样，流提供 count 方法来统计其中的元素个数,该方法返回一个`long`值代表元素个数（不再像旧集合那样是int值）。

```java
long count();
```

```java
import java.util.stream.Stream;

public class Demo09StreamCount {
    public static void main(String[] args) { 
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若"); 
        Stream<String> result = original.filter(s ‐> s.startsWith("张"));
        System.out.println(result.count()); // 2 
    }
}
```

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]