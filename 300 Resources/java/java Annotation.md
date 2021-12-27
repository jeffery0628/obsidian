---
Create: 2021年 十月 28日, 星期四 13:11
tags: 
  - Engineering/java
  - 大数据
---
# 注解

Annotation 可以像修饰符一样被使用, 可用于修饰包，类, 构造器, 方法, 成员变量, 参数, 局部变量。相当于给它们添加了额外的辅助信息，而且有些注解我们可以通过反射机制编程实现对这些元数据的访问。

## 注解的作用

1. 注解不是程序本身，可以对程序作出解释。（这一点，跟注释没什么区别）
2. 可以被其他程序（比如：编译器，Checker Framework等）读取。（注解信息处理流程，是注解和注释的重大区别。如果没有注解信息处理流程，则注解毫无意义）

## 格式

注解是以“@注释名”在代码中存在的，还可以添加一些参数值，例如：

```java
@SuppressWarnings(value=”unchecked”)
```

## 常见注解

### 生成文档

1. @author 标明开发该类模块的作者，多个作者之间使用,分割
2. @version 标明该类模块的版本
3. @see 参考转向，也就是相关主题
4. @since 从哪个版本开始增加的
5. @param 对方法中某参数的说明，如果没有参数就不能写
6. @return 对方法返回值的说明，如果方法的返回值类型是void就不能写
7. @exception 对方法可能抛出的异常进行说明 ，如果方法没有用throws显式抛出的异常就不能写

> 其中 @param  @return 和 @exception 这三个标记都是只用于方法的。
>
> @param的格式要求：@param 形参名 形参类型  形参说明
>
> @return 的格式要求：@return 返回值类型 返回值说明
>
> @exception的格式要求：@exception 异常类型 异常说明
>
> @param和@exception可以并列多个

```java
/**
 * 
 * @author Jeffery
 * @version 1.0
 * @see Math.java
 *
 */
public class TestJavadoc {

	/**
	 * 程序的主方法，程序的入口
	 * @param args String[] 命令行参数
	 */
	public static void main(String[] args) {
	}
	
	/**
	 * 求圆面积的方法
	 * @param radius double 半径值
	 * @return double 圆的面积
	 */
	public static double getArea(double radius){
		return Math.PI * radius * radius;
	}
}
```

### 在编译时进行格式检查

JDK中系统内置了常用的三个注解：

1. @Override：按照重写的要求检查方法的格式
2. @Deprecated：过时，表示不鼓励程序员使用这样的元素，因为存在危险或有更好的实现
3. @SuppressWarnings：抑制警告

> @SuppressWarnings,需要添加一个参数才能正确使用
>
> | 参数        | 说明                                               |
> | ----------- | -------------------------------------------------- |
> | deprecation | 使用了过时的类或方法的警告                         |
> | unchecked   | 执行了未检查的转换时的警告，如使用集合时未指定泛型 |
> | fallthrough | 当在switch语句使用时，发生case穿透                 |
> | path        | 在类路径、源文件路径等中有不存在路径的警告         |
> | serial      | 当在可序列化的类上缺少serialVersionUID定义时的警告 |
> | finally     | 任何finally子句不能完成时的警告                    |
> | all         | 关于以上所有情况的警告                             |
>
> ```java
> @SuppressWarnings("unchecked")
> @SuppressWarnings(value={"unchecked","deprecation"})
> ```



### 跟踪代码依赖性，实现替代配置文件功能

比如：spring框架中关于“事务”的管理

```java
@Transactional(propagation=Propagation.REQUIRES_NEW,
				isolation=Isolation.READ_COMMITTED,
				readOnly=false,
                timeout=3)
public void buyBook(String username, String isbn) {
    //1.查询书的单价
    int price = bookShopDao.findBookPriceByIsbn(isbn);
    //2. 更新库存
    bookShopDao.updateBookStock(isbn);	
    //3. 更新用户的余额
    bookShopDao.updateUserAccount(username, price);
}
```

```xml
 <!-- 配置事务属性 -->
<tx:advice transaction-manager="dataSourceTransactionManager" id="txAdvice">
    <tx:attributes>
        <!-- 配置每个方法使用的事务属性 -->
        <tx:method name="buyBook" propagation="REQUIRES_NEW" 
                   isolation="READ_COMMITTED"  read-only="false" 
                   timeout="3" />
    </tx:attributes>
</tx:advice>
```

### JUnit框架中的注解

使用JUnit测试的类必须是public的。JUnit4常见的注解和要求：这些方法都必须是public，无参，无返回值。

1. @Test：标记在非静态的测试方法上。只有标记@Test的方法才能被作为一个测试方法单独测试。一个类中可以有多个@Test标记的方法。运行时如果只想运行其中一个@Test标记的方法，那么选择这个方法名，然后单独运行，否则整个类的所有标记了@Test的方法都会被执行。
2. @Test(timeout=1000)：设置超时时间，如果测试时间超过了你定义的timeout，测试失败
3. @Test(expected)： 申明出会发生的异常，比如 @Test（expected = Exception.class）

> 了解：
>
> @BeforeClass：标记在静态方法上。因为这个方法只执行一次。在类初始化时执行。
>
> @AfterClass：标记在静态方法上。因为这个方法只执行一次。在所有方法完成后执行。
>
> @Before：标记在非静态方法上。在@Test方法前面执行，而且是在每一个@Test方法前面都执行
>
> @After：标记在非静态方法上。在@Test方法后面执行，而且是在每一个@Test方法后面都执行
>
> @Ignore：标记在本次不参与测试的方法上。这个注解的含义就是“某些方法尚未完成，暂不参与此次测试”。
>
> @BeforeClass、@AfterClass、@Before、@After、@Ignore都是配合@Test它使用的，单独使用没有意义。

```java
import org.junit.After;
import org.junit.AfterClass;
import org.junit.Before;
import org.junit.BeforeClass;
import org.junit.Test;
public class TestJUnit2 {
	private static Object[] array;
	private static int total;
	
	@BeforeClass
	public static void init(){
		System.out.println("初始化数组");
		array = new Object[5];
	}
	
	@Before
	public void before(){
		System.out.println("调用之前total=" + total);
	}
	
	@Test
	public void add(){
		//往数组中存储一个元素
		System.out.println("add");
		array[total++] = "hello";
	}
	
	@After
	public void after(){
		System.out.println("调用之前total=" + total);
	}
	
	@AfterClass
	public static void destroy(){
		array = null;
		System.out.println("销毁数组");
	}
}
```





## 自定义注解与反射读取注解

1. 定义新的 Annotation 类型使用 @interface 关键字
2. Annotation 的成员变量在 Annotation 定义中以无参数方法的形式来声明。其方法名和返回值定义了该成员的名字和类型，称为配置参数。类型只能是八种基本数据类型、String类型、Class类型、enum类型、Annotation类型、以上所有类型的数组。
3. 可以在定义 Annotation 的成员变量时为其指定初始值, 指定成员变量的初始值可使用 default 关键字
4. 如果只有一个参数成员，建议使用参数名为value
5. 如果定义的注解含有配置参数，那么使用时必须指定参数值，除非它有默认值。格式是“参数名 = 参数值”，如果只有一个参数成员，且名称为value，可以省略“value=”
6. 没有成员定义的 Annotation 称为标记; 包含成员变量的 Annotation 称为元数据 Annotation

> 注意：自定义注解必须配上注解的信息处理流程才有意义。

 ```java
package com.annotation.javadoc;

import java.lang.annotation.Annotation;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@MyAnnotation(value="ink")
public class TestMyAnnotation {

	public static void main(String[] args) {
		Class clazz = TestMyAnnotation.class;
		Annotation a = clazz.getAnnotation(MyAnnotation.class);
		MyAnnotation m = (MyAnnotation) a;
		String info = m.value();
		System.out.println(info);
	}

}
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface MyAnnotation{
	String value() default "jeffery";
}
 ```

## 元注解

Java5.0定义了4个标准的meta-annotation类型，它们被用来提供对其它 annotation类型作说明。

1. @Target
2. @Retention
3. @Documented
4. @Inherited

### @Target

用于描述注解的使用范围（即：被描述的注解可以用在什么地方）

| 取值           | 描述                       |
| -------------- | -------------------------- |
| CONSTRUCTOR    | 用于描述构造器             |
| FIELD          | 用于描述域                 |
| LOCAL_VARIABLE | 用于描述局部变量           |
| METHOD         | 用于描述方法               |
| PACKAGE        | 用于描述包                 |
| PARAMETER      | 用于描述参数               |
| TYPE           | 用于描述类、接口或enum声明 |

### @Retention

@Retention定义了该Annotation被保留的时间长短

作用：表示需要在什么级别保存该注释信息，用于描述注解的生命周期（即：被描述的注解在什么范围内有效）

@Rentention 包含一个 RetentionPolicy 类型的成员变量, 使用 @Rentention 时必须为该 value 成员变量指定值:

取值（RetentionPoicy）有：

1. SOURCE:在源文件中有效（即源文件保留）
2. CLASS:在class文件中有效（即class保留） 这是默认值
3. RUNTIME:在运行时有效（即运行时保留）当运行 Java 程序时, JVM 会保留注释. 程序可以通过反射获取该注释

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-enum-annotation/wps6hER9c.jpg)



### @Documented

Documented 注解表明这个注解应该被 javadoc工具记录。默认情况下,javadoc是不包括注解的，但如果声明注解时指定了 @Documented,则它会被 javadoc 之类的工具处理。



### @Inherited

允许子类继承父类中的注解



## JDK1.8注解的新特性

Java 8对注解处理提供了两点改进：可重复的注解及可用于类型的注解。此外，反射也得到了加强，在Java8中能够得到方法参数的名称。这会简化标注在方法参数上的注解。

### 可重复注解

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Target;

public class TestNewAnnotation {
	@LimitAnnotation(role="admin")
	@LimitAnnotation(role="manager")
	@LimitAnnotation(role="saler")
	public void test(){
	}
}

@Target(ElementType.METHOD)      此处的target必须与LimitAnnotation一致
@interface LimitAnnotations{
	LimitAnnotation[] value();
}

@Repeatable(LimitAnnotations.class)
@Target(ElementType.METHOD)
@interface LimitAnnotation{
	String role() default "admin";
}
```

### 类型注解

```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Target;

public class TestTypeDefine<@TypeDefine() U> {
	private U u;
	public <@TypeDefine() T> void test(T t){
		
	}

}
@Target({ElementType.TYPE_PARAMETER})
@interface TypeDefine{
}
```

> 类型注解被用来支持在Java的程序中做强类型检查。配合第三方插件工具Checker Framework（使用Checker Framework可以找到类型注解出现的地方并检查），可以在编译的时候检测出runtime error（eg：UnsupportedOperationException； NumberFormatException；NullPointerException异常等都是runtime error），以提高代码质量。这就是类型注解的作用

```java
package checker;

import org.checkerframework.checker.nullness.qual.NonNull;

public class TestChecker {
	public static void main(String[] args) {
		Object obj = null;
		printNonNullToString(obj);
	}

	public static void printNonNullToString(@NonNull Object object) {
		System.out.println(object.toString());
	}

}
```




[[200 Areas/230 Engineering/232 大数据/java|java 目录]]