---
Create: 2021年 十月 30日, 星期六 14:39
tags: 
  - Engineering/java
  - 大数据
---
## Predicate接口

有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果。这时可以使用`java.util.function.Predicate<T>` 接口。

### 抽象方法：test

`Predicate` 接口中包含一个抽象方法：` boolean test(T t) `。用于条件判断的场景：

```java
import java.util.function.Predicate;

public class Demo15PredicateTest {
    private static void method(Predicate<String> predicate) { 
        boolean veryLong = predicate.test("HelloWorld"); 
        System.out.println("字符串很长吗：" + veryLong); 
    }
    
    public static void main(String[] args) { 
        method(s ‐> s.length() > 5); 
    }

}
```

条件判断的标准是传入的Lambda表达式逻辑。

### 默认方法：and

既然是条件判断，就会存在与、或、非三种常见的逻辑关系。其中将两个 `Predicate` 条件使用“与”逻辑连接起来实现“并且”的效果时，可以使用`default`方法 `and` 。其JDK源码为：

```java
default Predicate<T> and(Predicate<? super T> other) { 
    Objects.requireNonNull(other); 
    return (t) ‐> test(t) && other.test(t); 
}
```

例1：如果要判断一个字符串既要包含大写“H”，又要包含大写“W”。

```java
import java.util.function.Predicate;

public class Demo16PredicateAnd {
    private static void method(Predicate<String> one, Predicate<String> two) { 
        boolean isValid = one.and(two).test("Helloworld"); 
        System.out.println("字符串符合要求吗：" + isValid); 
    }
    
    public static void main(String[] args) { 
        method(s ‐> s.contains("H"), s ‐> s.contains("W")); 
    }
}
```

### 默认方法：or

与 `and` 的“与”类似，默认方法 or 实现逻辑关系中的“或”。JDK源码为：

```java
default Predicate<T> or(Predicate<? super T> other) { 
    Objects.requireNonNull(other); 
    return (t) ‐> test(t) || other.test(t); 
}
```

例2：如果希望实现逻辑“字符串包含大写H或者包含大写W”，那么代码只需要将“and”修改为“or”名称即可，其他都不变：

```java
import java.util.function.Predicate;

public class Demo16PredicateAnd {
    private static void method(Predicate<String> one, Predicate<String> two) { 
        boolean isValid = one.or(two).test("Helloworld"); 
        System.out.println("字符串符合要求吗：" + isValid); 
    }
    
    public static void main(String[] args) { 
        method(s ‐> s.contains("H"), s ‐> s.contains("W")); 
    }
}
```

### 默认方法：negate

“与”、“或”已经了解了，剩下的“非”（取反）也会简单。默认方法 negate 的JDK源代码为：

```java
default Predicate<T> negate() { 
    return (t) ‐> !test(t); 
}
```

从实现中很容易看出，它是执行了`test`方法之后，对结果`boolean`值进行“!”取反而已。一定要在 `test` 方法调用之前调用 `negate` 方法，正如 and 和 or 方法一样：

```java
import java.util.function.Predicate;

public class Demo17PredicateNegate {
    private static void method(Predicate<String> predicate) { 
        boolean veryLong = predicate.negate().test("HelloWorld"); 
        System.out.println("字符串很长吗：" + veryLong); 
    }
    
    public static void main(String[] args) { 
        method(s ‐> s.length() < 5); 
    }
}
```



[[200 Areas/230 Engineering/231 java|java 目录]]



