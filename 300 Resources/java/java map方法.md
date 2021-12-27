---
Create: 2021年 十月 30日, 星期六 14:46
tags: 
  - Engineering/java
  - 大数据
---

## map

如果需要将流中的元素映射到另一个流中，可以使用 `map` 方法。该接口需要一个 Function 函数式接口参数，可以将当前流中的`T`类型数据转换为另一种`R`类型的流。

```java
<R> Stream<R> map(Function<? super T, ? extends R> mapper);
```

> `java.util.stream.Function `函数式接口，其中唯一的抽象方法为：`R apply(T t);`可以将一种T类型转换成为R类型，而这种转换的动作，就称为“映射”。

```java
import java.util.stream.Stream;

public class Demo08StreamMap { 
    public static void main(String[] args) { 
        Stream<String> original = Stream.of("10", "12", "18"); 
        Stream<Integer> result = original.map(str‐>Integer.parseInt(str)); 
    } 
}
```

`map` 方法的参数通过方法引用，将字符串类型转换成为了int类型（并自动装箱为 `Integer` 类对 象）。





[[200 Areas/230 Engineering/232 大数据/java|java 目录]]