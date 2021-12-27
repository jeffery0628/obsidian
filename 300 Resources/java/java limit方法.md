---
Create: 2021年 十月 30日, 星期六 14:54
tags: 
  - 

---
## limit

`limit` 方法可以对流进行截取，只取用前`n`个。参数是一个`long`型，如果集合当前长度大于参数则进行截取；否则不进行操作。

```java
Stream<T> limit(long maxSize);
```

```java
import java.util.stream.Stream;

public class Demo10StreamLimit {
    public static void main(String[] args) { 
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若"); 
        Stream<String> result = original.limit(2); 
        System.out.println(result.count()); // 2 
    }
}
```





[[200 Areas/230 Engineering/232 大数据/java|java 目录]]