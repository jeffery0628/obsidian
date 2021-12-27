---
Create: 2021年 十月 30日, 星期六 14:40
tags: 
  - Engineering/java
  - 大数据
---
## Function接口

`java.util.function.Function<T,R>` 接口用来根据一个类型的数据得到另一个类型的数据，前者称为前置条件，后者称为后置条件。

### 抽象方法：apply

`Function` 接口中最主要的抽象方法为：` R apply(T t) `，根据类型T的参数获取类型R的结果。

使用的场景例如：将 `String` 类型转换为 `Integer` 类型。

```java
import java.util.function.Function;

public class Demo11FunctionApply {
    private static void method(Function<String, Integer> function) { 
        int num = function.apply("10"); 
        System.out.println(num + 20); 
    }
    
    public static void main(String[] args) { 
        method(s ‐> Integer.parseInt(s)); 
    }
}
```

> 当然，最好是通过方法引用的写法。

### 默认方法：andThen

`Function` 接口中有一个默认的 `andThen` 方法，用来进行组合操作。JDK源代码如：

```java
default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
    Objects.requireNonNull(after);
    return (T t) ‐> after.apply(apply(t)); 
}
```

```java
import java.util.function.Function;

public class Demo12FunctionAndThen {
    private static void method(Function<String, Integer> one, Function<Integer, Integer> two) { 
        int num = one.andThen(two).apply("10"); 
        System.out.println(num + 20); 
    }
    
    public static void main(String[] args) { 
        method(str‐>Integer.parseInt(str)+10, i ‐> i *= 10); 
    }
}
```

第一个操作是将字符串解析成为int数字，第二个操作是乘以10。两个操作通过 `andThen` 按照前后顺序组合到了一起。

> 请注意，Function的前置条件泛型和后置条件泛型可以相同。





[[200 Areas/230 Engineering/232 大数据/java|java 目录]]

