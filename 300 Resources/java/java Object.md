---
Create: 2021年 十月 27日, 星期三 22:51
tags: 
  - Engineering/java
  - 大数据
---
# Object 类

`java.lang.Object`类是Java语言中的根类，即所有类的父类。它中描述的所有方法子类都可以使用。在对象实例化的时候，最终找的父类就是Object。如果一个类没有特别指定父类， 那么默认则继承自Object类。

 public class MyClass /*extends Object*/ {  
  // ...  
 }

根据JDK源代码及Object类的API文档，Object类当中包含的方法有11个。主要学习其中的2个：

-   `public String toString()`：返回该对象的字符串表示。
  
-   `public boolean equals(Object obj)`：指示其他某个对象是否与此对象“相等”。

## toString方法

 `public String toString()`：返回该对象的字符串表示。
    

`toString`方法返回该对象的字符串表示，其实该字符串内容就是`对象的类型+@+内存地址值`。

由于toString方法返回的结果是内存地址，而在开发中，经常需要按照对象的属性得到相应的字符串表现形式，因此也需要重写它。

### 覆盖重写

如果不希望使用toString方法的默认行为，则可以对它进行覆盖重写。例如自定义的Person类：

```java
 public class Person {   
     private String name;  
     private int age;  
     
     @Override  
     public String toString() {  
         return "Person{" + "name='" + name + '\'' + ", age=" + age + '}';  
     }  
     // 省略构造器与Getter Setter...  
 }
```

在IntelliJ IDEA中，可以点击`Code`菜单中的`Generate...`，点击`toString()`选项。选择需要包含的成员变量并确定。

> 在我们直接使用输出语句输出对象名的时候,其实通过该对象调用了其toString()方法。

## equals方法

`public boolean equals(Object obj)`：指示其他某个对象是否与此对象“相等”。

调用成员方法equals并指定参数为另一个对象，则可以判断这两个对象是否是相同的。这里的“相同”有默认和自定义两种方式。

### 默认地址比较

如果没有覆盖重写equals方法，那么Object类中默认进行`==`运算符的对象`地址比较`，只要不是同一个对象，结果必然为false。

### 对象内容比较

如果希望进行对象的内容比较，即所有或指定的部分成员变量相同就判定两个对象相同，则可以`覆盖重写equals`方法。例如：

```java
import java.util.Objects;  
public class Person {   
    private String name;  
    private int age;  
    
    @Override  
    public boolean equals(Object o) {  
        // 如果对象地址一样，则认为相同  
        if (this == o)  
            return true;  
        
        // 如果参数为空，或者类型信息不一样，则认为不同  
        if (o == null || getClass() != o.getClass())  
            return false;  
        // 转换为当前类型  
        Person person = (Person) o;  
        // 要求基本类型相等，并且将引用类型交给java.util.Objects类的equals静态方法取用结果  
        return age == person.age && Objects.equals(name, person.name);  
    }  
}
```

这段代码充分考虑了对象为空、类型一致等问题，但方法内容并不唯一。大多数IDE都可以自动生成equals方法的代码内容。在IntelliJ IDEA中，可以使用`Code`菜单中的`Generate…`选项，并选择`equals() and hashCode()`进行自动代码生成。

在**JDK7**添加了一个Objects工具类，它提供了一些方法来操作对象，它由一些静态的实用方法组成，这些方法是`null-safe`（空指针安全的）或`null-tolerant`（容忍空指针的），用于计算对象的`hashcode`、返回对象的字符串表示形式、比较两个对象。

在比较两个对象的时候，`Object`的`equals`方法容易抛出空指针异常，而`Objects`类中的`equals`方法就优化了这个问题。方法如下：

`public static boolean equals(Object a, Object b)`:判断两个对象是否相等。

```java
public static boolean equals(Object a, Object b) {   
  return (a == b) || (a != null && a.equals(b));   
 }
```


[[200 Areas/230 Engineering/231 java|java 目录]]


