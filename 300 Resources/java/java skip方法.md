---
Create: 2021年 十月 30日, 星期六 14:54
tags: 
  - 

---
## skip

如果希望跳过前几个元素，可以使用 skip 方法获取一个截取之后的新流,如果流的当前长度大于n，则跳过前n个；否则将会得到一个长度为0的空流。

```java
Stream<T> skip(long n);
```

```java
import java.util.stream.Stream;

public class Demo11StreamSkip {
    public static void main(String[] args) { 
        Stream<String> original = Stream.of("张无忌", "张三丰", "周芷若"); 
        Stream<String> result = original.skip(2); 
        System.out.println(result.count()); // 1 
    }
}
```



[[200 Areas/230 Engineering/231 java|java 目录]]