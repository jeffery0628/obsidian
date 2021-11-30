---
Create: 2021年 十月 30日, 星期六 14:04
tags: 
  - Engineering/java
  - 大数据
---
# lambda方法引用

在使用Lambda表达式的时候，实际上传递进去的代码就是一种解决方案：拿什么参数做什么操作。

那么考虑一种情况：如果在Lambda中所指定的操作方案，已经有地方存在相同方案，那是否还有必要再写重复逻辑？

## 冗余的Lambda场景

一个简单的函数式接口以应用Lambda表达式：

```java
@FunctionalInterface 
public interface Printable { 
    void print(String str); 
}
```

在 `Printable` 接口当中唯一的抽象方法 `print` 接收一个字符串参数，目的就是为了打印显示它。那么通过`Lambda` 来使用它的代码很简单：

```java
public class Demo01PrintSimple {
    private static void printString(Printable data) { 
        data.print("Hello, World!"); 
    }
    
    public static void main(String[] args) { 
        printString(s ‐> System.out.println(s)); 
    }
}
```

其中 `printString` 方法只管调用 `Printable` 接口的 `print` 方法，而并不管 `print` 方法的具体实现逻辑会将字符串打印到什么地方去。而 main 方法通过Lambda表达式指定了函数式接口 Printable 的具体操作方案为：拿到 String（类型可推导，所以可省略）数据后，在控制台中输出它。

这段代码的问题在于，对字符串进行控制台打印输出的操作方案，明明已经有了现成的实现，那就是 `System.out` 对象中的 `println` 方法。既然`Lambda`希望做的事情就是调用 `println(String) `方法，那何必自己手动调用呢？

能否省去`Lambda`的语法格式（尽管它已经相当简洁）呢？只要“引用”过去就好了：

```java
public class Demo02PrintRef {
    private static void printString(Printable data) { 
        data.print("Hello, World!"); 
    }
    
    public static void main(String[] args) { 
        printString(System.out::println); 
    }
}
```

> 双冒号 :: 写法，这被称为“方法引用”，而双冒号是一种新的语法。

## 方法引用符

双冒号` :: `为引用运算符，而它所在的表达式被称为方法引用。如果Lambda要表达的函数方案已经存在于某个方法的实现中，那么则可以通过双冒号来引用该方法作为Lambda的替代者。

例如上例中， `System.out `对象中有一个重载的 `println(String)` 方法恰好就是我们所需要的。那么对于 `printString `方法的函数式接口参数，对比下面两种写法，完全等效：

- Lambda表达式写法： `s -> System.out.println(s)`

- 方法引用写法： `System.out::println`

第一种语义是指：拿到参数之后经`Lambda`之手，继而传递给 `System.out.println` 方法去处理。 

第二种等效写法的语义是指：直接让 `System.out` 中的 `println` 方法来取代`Lambda`。

两种写法的执行效果完全一样，而第二种方法引用的写法复用了已有方案，更加简洁。 

> 注:Lambda 中传递的参数一定是方法引用中的那个方法可以接收的类型,否则会抛出异常

### 推导与省略

如果使用`Lambda`，那么根据“可推导就是可省略”的原则，无需指定参数类型，也无需指定的重载形式——它们都将被自动推导。而如果使用方法引用，也是同样可以根据上下文进行推导。 函数式接口是Lambda的基础，而方法引用是`Lambda`的`孪生兄弟`。 下面这段代码将会调用 println 方法的不同重载形式，将函数式接口改为int类型的参数：

```java
@FunctionalInterface 
public interface PrintableInteger { 
    void print(int str); 
}
```

由于上下文变了之后可以自动推导出唯一对应的匹配重载，所以方法引用没有任何变化：

```java
public class Demo03PrintOverload {
    private static void printInteger(PrintableInteger data) { 
        data.print(1024); 
    }
    
    public static void main(String[] args) { 
        printInteger(System.out::println); 
    }
}
```

这次方法引用将会自动匹配到 println(int) 的重载形式。

### 通过对象名引用成员方法

这是最常见的一种用法，与上例相同。如果一个类中已经存在了一个成员方法：

```java
public class MethodRefObject { 
    public void printUpperCase(String str) {
        System.out.println(str.toUpperCase()); 
    } 
}
```

函数式接口仍然定义为：

```java
@FunctionalInterface 
public interface Printable { 
    void print(String str); 
}
```

那么当需要使用这个 `printUpperCase` 成员方法来替代 `Printable` 接口的`Lambda`的时候，已经具有了 `MethodRefObject` 类的对象实例，则可以通过对象名引用成员方法，代码为：

```java
public class Demo04MethodRef {
    private static void printString(Printable lambda) { 
        lambda.print("Hello"); 
    }
    
    public static void main(String[] args) { 
        MethodRefObject obj = new MethodRefObject(); 
        printString(obj::printUpperCase); 
    }
}
```

### 通过类名称引用静态方法

由于在` java.lang.Math `类中已经存在了静态方法 `abs` ，所以当我们需要通过`Lambda`来调用该方法时，有两种写 法。

首先是函数式接口：

```java
@FunctionalInterface 
public interface Calcable { 
    int calc(int num); 
}
```

第一种写法是使用`Lambda`表达式：

```java
public class Demo05Lambda {
    private static void method(int num, Calcable lambda) { 
        System.out.println(lambda.calc(num)); 
    }
    
    public static void main(String[] args) { 
        method(‐10, n ‐> Math.abs(n)); 
    }
}
```

但是使用方法引用的更好写法是：

```java
public class Demo06MethodRef {
    private static void method(int num, Calcable lambda) { 
        System.out.println(lambda.calc(num)); 
    }
    
    public static void main(String[] args) { 
        method(‐10, Math::abs); 
    }
}
```

两种写法是等效的：

- Lambda表达式：` n -> Math.abs(n) `
- 方法引用： `Math::abs`

### 通过super引用成员方法

如果存在继承关系，当Lambda中需要出现super调用时，也可以使用方法引用进行替代。

首先是函数式接口：

```java
@FunctionalInterface 
public interface Greetable { 
    void greet(); 
}
```

然后是父类 Human 的内容：

```java
public class Human {
    public void sayHello() { 
        System.out.println("Hello!"); 
    }
}
```

最后是子类 `Man` 的内容，其中使用了`Lambda`的写法：

```java
public class Man extends Human {
    @Override 
    public void sayHello() { 
        System.out.println("大家好,我是Man!"); 
    }
    
    //定义方法method,参数传递Greetable接口 
    public void method(Greetable g){ 
        g.greet(); 
    }
    
    public void show(){
        //调用method方法,使用Lambda表达式 
        method(()‐>{
            //创建Human对象,调用sayHello方法
            new Human().sayHello(); }); 
        //简化Lambda 
        method(()‐>new Human().sayHello()); 
        //使用super关键字代替父类对象 
        method(()‐>super.sayHello());
    }
}
```

但是如果使用方法引用来调用父类中的 sayHello 方法会更好，例如另一个子类 Woman ：

```java
public class Man extends Human {
    @Override 
    public void sayHello() { 
        System.out.println("大家好,我是Man!"); 
    }
    
    //定义方法method,参数传递Greetable接口 
    public void method(Greetable g){ 
        g.greet(); 
    }
    
    public void show(){ 
        method(super::sayHello); 
    }
}
```

下面两种写法是等效的：

- Lambda表达式： `() -> super.sayHello() `
- 方法引用： `super::sayHello`

### 通过this引用成员方法

this代表当前对象，如果需要引用的方法就是当前类中的成员方法，那么可以使用`this::成员方法`的格式来使用方 法引用。首先是简单的函数式接口：

```java
@FunctionalInterface 
public interface Richable { 
    void buy(); 
}
```

```java
public class Husband {
    private void marry(Richable lambda) { 
        lambda.buy(); 
    }
    
    public void beHappy() { 
        marry(() ‐> System.out.println("买套房子")); }

}
```

开心方法 `beHappy` 调用了结婚方法 marry ，后者的参数为函数式接口 `Richable` ，所以需要一个Lambda表达式。 但是如果这个Lambda表达式的内容已经在本类当中存在了，则可以对 Husband 丈夫类进行修改：

```java
public class Husband {
    private void buyHouse() { 
        System.out.println("买套房子"); 
    }
    
    private void marry(Richable lambda) { 
        lambda.buy(); 
    }
    
    public void beHappy() { 
        marry(() ‐> this.buyHouse()); 
    }

}
```

如果希望取消掉Lambda表达式，用方法引用进行替换，则更好的写法为：

```java
public class Husband {
    
    private void buyHouse() { 
        System.out.println("买套房子"); 
    }
    
    private void marry(Richable lambda) { 
        lambda.buy(); 
    }
    
    public void beHappy() { 
        marry(this::buyHouse); 
    }

}
```

下面两种写法是等效的：

- Lambda表达式：` () -> this.buyHouse()` 
- 方法引用： `this::buyHouse`

### 类的构造器引用

由于构造器的名称与类名完全一样，并不固定。所以构造器引用使用 `类名称::new `的格式表示。首先是一个简单的 Person 类：

```java
public class Person { 
    private String name;
    
    public Person(String name) { 
        this.name = name; 
    }
    
    public String getName() { 
        return name; 
    }
    
    public void setName(String name) { 
        this.name = name; 
    }
}
```

然后是用来创建 Person 对象的函数式接口：

```java
public interface PersonBuilder { 
    Person buildPerson(String name); 
}
```

要使用这个函数式接口，可以通过Lambda表达式：

```java
public class Demo09Lambda {
    
    public static void printName(String name, PersonBuilder builder) { 
        System.out.println(builder.buildPerson(name).getName()); 
    }
    
    public static void main(String[] args) { 
        printName("赵丽颖", name ‐> new Person(name)); 
    }

}
```

但是通过构造器引用，有更好的写法：

```java
public class Demo10ConstructorRef {
    
    public static void printName(String name, PersonBuilder builder) {
        System.out.println(builder.buildPerson(name).getName()); 
    }
    
    public static void main(String[] args) { 
        printName("赵丽颖", Person::new); 
    }

}
```

下面两种写法是等效的：

- Lambda表达式： `name -> new Person(name)` 
- 方法引用：` Person::new`

### 数组的构造器引用

数组也是 Object 的子类对象，所以同样具有构造器，只是语法稍有不同。如果对应到Lambda的使用场景中时， 需要一个函数式接口：

```java
@FunctionalInterface 
public interface ArrayBuilder { 
    int[] buildArray(int length); 
}
```

在应用该接口的时候，可以通过Lambda表达式：

```java
public class Demo11ArrayInitRef {
    private static int[] initArray(int length, ArrayBuilder builder) { 
        return builder.buildArray(length); 
    }
    
    public static void main(String[] args) { 
        int[] array = initArray(10, length ‐> new int[length]); 
    }

}
```

但是更好的写法是使用数组的构造器引用：

```java
public class Demo12ArrayInitRef {
    private static int[] initArray(int length, ArrayBuilder builder) { 
        return builder.buildArray(length); 
    }
    
    public static void main(String[] args) { 
        int[] array = initArray(10, int[]::new); 
    }
}
```

下面两种写法是等效的：

- Lambda表达式： `length -> new int[length]` 
- 方法引用： `int[]::new`



[[200 Areas/230 Engineering/231 java|java 目录]]



