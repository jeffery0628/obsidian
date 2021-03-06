---
Create: 2021年 十月 30日, 星期六 11:52
tags: 
  - Engineering/java
  - 大数据
---





# 继承

一个类从另外一个类继承所有成员, 包括属性和方法, 但是不包括构造器和语句块从现有类创建子类, 现有类就称为父类, 基类, 超类。

多个类中存在相同属性和行为时，将这些内容抽取到单独一个类中，那么多个类无需再定义这些属性和行为，只要继承那个类即可。

## 继承语法

```java
class 子类 extends 父类 {}
```

> 从语法意思来看, 子类是扩展自父类, 也可以理解为子类是在以父类为基础的前提下, 进一步扩展一些属性和方法, 所以子类大于父类, 或者也可以说, 子类包含父类.

## 继承的特点

1. 子类继承父类的所有成员(**构造器除外**), 就意味着父类的私有成员也会被子类继承, 但是因为私有成员只能被本类访问, 所以即使是在子类中也不能直接访问从父类继承的私有成员, 可以通过从父类继承的公共的get/set方法间接访问。
2. 单继承：一个类不允许有多个直接父类, 间接父类并没有个数限制，称之为单继承。

## 方法覆盖

在子类中可以根据需要对从父类中继承来的方法进行改造，也称方法的重写、重置。在程序执行时，子类的方法将覆盖父类的方法。

要求：

1. 覆盖方法必须和被重写方法具有相同的方法名称、参数列表和返回值类型。
2. 覆盖方法不能使用比被重写方法更严格的访问权限。
3. 覆盖和被覆盖的方法必须同时为非static的。
4. 子类方法抛出的异常不能大于父类被重写方法的异常

方法覆盖的特殊性：

子类一旦把父类的方法覆盖, 那么在测试类中再无法通过子类对象调用父类的被覆盖方法了, 因为子类已经把父类的方法重写了, 如果有调用父类方法的需求, 只能通过再创建一个父类对象来调用。



@Override 注解的使用

在子类中写重写方法时, 容易出现一些低级的拼写错误, 或其他错误, 导致方法不能正确覆盖时, 程序的运行就会出乎意外, 所以为了防止这种现象, 在子类的覆盖方法上添加修饰符@Override注解。

> 注解 : 本质上一种类, 也是一种特殊的注释, 所以一般情况下,  程序不执行注解, 但是会被编译器, 运行时所识别和处理(通过反射的方式).注解也有很多.
>
> @Override注解的作用是告诉编译器, 在编译程序时, 必须先检查此方法是否满足方法覆盖的条件, 如果不满足, 则编译出错, 这样强制程序员通过排查, 提前检查方法覆盖的问题.



## super 关键字

在Java类中使用super来调用父类中的指定操作：

1. super可用于访问父类中定义的属性
2. super可用于调用父类中定义的成员方法
3. super可用于在子类构造方法中调用父类的构造器

> 注：
>
> 1. 尤其当子父类出现同名成员时，可以用super进行区分
> 2. super的追溯不仅限于直接父类
> 3. super和this的用法相像，this代表本类对象的引用，super代表父类的内存空间的标识
> 4. super关键字表示在当前类中特别指定要使用父类的成员时使用super限定。这里的父类不仅包括直接父类, 也包括间接父类。



super 和 this的区别

| 区别点     | this                                                   | super                                        |
| ---------- | ------------------------------------------------------ | -------------------------------------------- |
| 访问属性   | 访问本类中的属性，如果本类没有此属性则从父类中继续查找 | 访问父类中的属性                             |
| 调用方法   | 访问本类中的方法                                       | 直接访问父类中的方法                         |
| 调用构造器 | 调用本类构造器，必须放在构造器的首行                   | 调用直接父类构造器，必须放在子类构造器的首行 |
| 特殊       | 表示当前对象                                           | 无此概念                                     |

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]