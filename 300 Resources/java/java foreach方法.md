---
Create: 2021年 十月 30日, 星期六 14:52
tags: 
  - 

---
## forEach

虽然方法名字叫 `forEach` ，但是与`for`循环中的`for-each`不同。该方法接收一个 `Consumer` 接口函数，会将每一个流元素交给该函数进行处理。

```java
void forEach(Consumer<? super T> action);
```

> `java.util.function.Consumer<T>`接口是一个消费型接口。 `Consumer`接口中包含抽象方法`void accept(T t)`，意为消费一个指定泛型的数据。

```java
import java.util.stream.Stream;

public class Demo12StreamForEach { 
    public static void main(String[] args) { 
        Stream<String> stream = Stream.of("张无忌", "张三丰", "周芷若"); 
        stream.forEach(name‐> System.out.println(name)); 
    } 
}
```



[[200 Areas/230 Engineering/232 大数据/java|java 目录]]