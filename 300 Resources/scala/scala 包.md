---
Create: 2022年 四月 10日, 星期日 18:26
tags: 
  - Engineering/scala
  - 大数据
---
Scala的面向对象思想和Java的面向对象思想和概念是一致的。
Scala中语法和Java不同，补充了更多的功能。

# scala包
package 包名.类名
Scala包的三大作用：
1.  区分相同名字的类
2.  当类很多时，可以很好的管理类
3.  控制访问范围


# 包的命名
命名规则：只能包含数字、字母、下划线、小圆点.，但不能用数字开头，也不要使用关键字。

```scala
	com.seu.oa.model  
	com.seu.oa.controller
	com.seu.bank.order 
```

错误示范：
```scala
	demo.class.exec1  //错误，因为 class 关键字
	demo.12a    //错误，数字开头
```

# 包说明
Scala有两种包的管理风格，一种方式和Java的包管理风格相同，每个源文件一个包（包名和源文件所在路径不要求必须一致），包名用“.”进行分隔以表示包的层级关系，如com.seu.scala。另一种风格，通过嵌套的风格表示层级关系，如下:

```scala
package com{
	package atguigu{
		package scala{

		}
	}
}
```

> 第二种风格有以下特点：
> 1. 一个源文件中可以声明多个package
> 2. 子包中的类可以直接访问父包中的内容，而无需导包

```scala
package com {

    import com.seu.Inner //父包访问子包需要导包

    object Outer {
        val out: String = "out"

        def main(args: Array[String]): Unit = {
            println(Inner.in)
        }
    }
    package seu {

        object Inner {
            val in: String = "in"

            def main(args: Array[String]): Unit = {
                println(Outer.out) //子包访问父包无需导包
            }
        }
    }
}

package other {

}
```

# 包对象
在Scala中可以为每个包定义一个同名的包对象，定义在包对象中的成员，作为其对应包下所有class和object的共享变量，可以被直接访问。

```scala
package object com{
	val shareValue="share"
	def shareMethod()={}
}
```

一、若使用Java的包管理风格，则包对象一般定义在其对应包下的package.scala文件中，包对象名与包名保持一致。
![[700 Attachments/Pasted image 20220410185041.png]]

二、如采用嵌套方式管理包，则包对象可与包定义在同一文件中，但是要保证包对象与包声明在同一作用域中。

```scala
package com {
    object Outer {
        val out: String = "out"
        def main(args: Array[String]): Unit = {
            println(name)
        }
    }
}

package object com {
    val name: String = "com"
}
```

# 导包说明

1. 和Java一样，可以在顶部使用import导入，在这个文件中的所有类都可以使用。
2. 局部导入：什么时候使用，什么时候导入。在其作用范围内都可以使用
3. 通配符导入：import java.util.\_
4. 给类起别名：import java.util.{ArrayList=>JL}
5. 屏蔽类：import java.util.{ArrayList =>\_,\_}
6. 导入**相同包的**多个类：import java.util.{HashSet, ArrayList}
7. 导入包的绝对路径：new \_root\_.java.util.HashMap

| import com.jeffery.Fruit              | 引入com.jeffery包下Fruit（class和object）             |
| ------------------------------------- | ----------------------------------------------------- |
| import com.jeffery._                  | 引入com.jeffery下的所有成员                           |
| import comjeffery.Fruit._             | 引入Fruit(object)的所有成员                           |
| import com.jeffery.{Fruit,Vegetable}  | 引入com.jeffery下的Fruit和Vegetable                   |
| import com.jeffery.{Fruit=>Shuiguo}   | 引入com.jeffery包下的Fruit并更名为Shuiguo             |
| import com.jeffery.{Fruit=>Shuiguo,\_} | 引入com.jeffery包下的所有成员，并将Fruit更名为Shuiguo |
| import com.jeffery.{Fruit=>\_,\_}       | 引入com.jeffery包下屏蔽Fruit类                        |
| new \_root\_.java.util.HashMap          | 引入的Java的绝对路径                                  |

> Scala中的三个默认导入分别是:
> 1. import java.lang.\_
> 2. import scala.\_
> 3. import scala.Predef.\_


# 访问权限

在Java中，访问权限分为：public，private，protected和默认。
在Scala中，你可以通过类似的修饰符达到同样的效果。但是使用上有区别。

1. Scala 中属性和方法的默认访问权限为public，但Scala中无public关键字。
2. private为私有权限，只在类的内部和伴生对象中可用。
3. protected为受保护权限，Scala中受保护权限比Java中更严格，同类、子类可以访问，同包无法访问。
4. private[包名]增加包访问权限，包名下的其他类也可以使用






