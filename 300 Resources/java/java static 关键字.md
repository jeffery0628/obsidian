---
Create: 2021年 十月 30日, 星期六 12:00
tags: 
  - Engineering/java
  - 大数据
---
## static 关键字

关于 `static` 关键字的使用，它可以用来修饰的成员变量和成员方法，被修饰的成员是属于类的，而不是单单是属于某个对象的。也就是说，既然属于类，就可以不靠创建对象来调用了。

### 类变量

类变量：使用 `static`关键字修饰的成员变量。该类的每个对象都共享同一个类变量的值。任何对象都可以更改该类变量的值，但也可以在不创建该类的对象的情况下对类变量进行操作。

```java
static 数据类型 变量名；
static int numberID；
```

### 静态方法

类方法：使用 `static`关键字修饰的成员方法，习惯称为静态方法。静态方法在声明中有 `static` ，建议使用类名来调用，而不需要创建类的对象。

```java
修饰符 static 返回值类型 方法名 (参数列表){ 
    // 执行语句 
}
//在Student类中定义静态方法
public static void showNum() { 
    System.out.println("num:" + )
}
```

> 静态方法调用注意事项（静态方法只能访问静态成员。）：
>
> - 静态方法可以直接访问类变量和静态方法。 
>
> - 静态方法不能直接访问普通成员变量或成员方法。反之，成员方法可以直接访问类变量或静态方法。 
>
> 	原因：因为在内存当中是【先】有的静态内容，【后】有的非静态内容。
>
> - 静态方法中，不能使用this关键字。
>
> 	原因：this代表当前对象，通过谁调用的方法，谁就是当前对象。

### 使用

被`static`修饰的成员可以并且建议通过类名直接访问。虽然也可以通过对象名访问静态成员，原因即多个对象均属于一个类，共享使用同一个静态成员，但是不建议，会出现警告信息。

```java
// 访问类变量 
类名.类变量名；

// 调用静态方法 
类名.静态方法名(参数)；
    
public class StuDemo2 {
    public static void main(String[] args) { 
        // 访问类变量 
        System.out.println(Student.numberOfStudent); 
        // 调用静态方法 
        Student.showNum(); 
    }

}
```

### 原理图解

`static` 修饰的内容：

- 是随着类的加载而加载的，且只加载一次。 
- 存储于一块固定的内存区域（静态区），所以，可以直接被类名调用。 
- 它优先于对象存在，所以，可以被所有对象共享。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-object-oriented/image-20200910234537685.png)



### 静态代码块

`静态代码块`：定义在成员位置，使用`static`修饰的代码块{ }。

- 位置：类中方法外。
- 执行：随着类的加载而`执行且执行一次`，`优先于main方法和构造方法的执行(静态内容总是优先于非静态，所以静态代码块比构造方法先执行。)`。

`作用`：给类变量进行初始化赋值。

```java
public class Game {
    public static int number; 
    public static ArrayList<String> list;
    static {
        // 给类变量赋值 
        number = 2; 
        list = new ArrayList<String>(); 
        // 添加元素到集合中 
        list.add("张三"); 
        list.add("李四");
    }
}
```

> static 关键字，可以修饰变量、方法和代码块。在使用的过程中，其主要目的还是想在不创建对象的情况下，去调用方法。

[[200 Areas/230 Engineering/231 java|java 目录]]