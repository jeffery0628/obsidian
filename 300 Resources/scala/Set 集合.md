---
Create: 2022年 四月 10日, 星期日 21:58
tags: 
  - Engineering/scala
  - 大数据
---
默认情况下，Scala使用的是不可变集合，如果想使用可变集合，需要引用 scala.collection.mutable.Set 包

# 不可变Set
1. Set默认是不可变集合，数据无序
2. 数据不可重复
3. 遍历集合

```scala
object TestSet {

  def main(args: Array[String]): Unit = {

    //（1）Set默认是不可变集合，数据无序
    val set = Set(1,2,3,4,5,6)

    //（2）数据不可重复
    val set1 = Set(1,2,3,4,5,6,3)

    //（3）遍历集合
    println(set1.mkString(","))  // 5,1,6,2,3,4
  }
}
```

## 可变mutable.Set
1. 创建可变集合mutable.Set
2. 打印集合
3. 集合添加元素
4. 向集合中添加元素，返回一个新的Set
5. 删除数据

```scala
import scala.collection.mutable

object TestSet {

  def main(args: Array[String]): Unit = {

    //（1）创建可变集合
    val set = mutable.Set(1,2,3,4,5,6)

    //（3）集合添加元素
    set += 8

    //（4）向集合中添加元素，返回一个新的Set
    val ints = set.+(9)
    println("ints="+ints.mkString(","))
    println("set2=" + set.mkString(","))

    //（5）删除数据
    set-=(5)  // 删除指定元素
    println(set.mkString(","))
  }
}
```






