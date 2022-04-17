---
Create: 2022年 四月 10日, 星期日 21:59
tags: 
  - Engineering/scala
  - 大数据
---

Scala中的Map和Java类似，也是一个散列表，它存储的内容也是键值对（key-value）映射

# 不可变Map

1. 创建不可变集合Map
2. 循环打印
3. 访问数据
4. 如果key不存在，返回0

```scala
object TestMap {
  def main(args: Array[String]): Unit = {
    // Map
    //（1）创建不可变集合Map
    val map = Map( "a"->1, "b"->2, "c"->3 )
    //（2）循环打印
    println(map.mkString(","))  // a -> 1,b -> 2,c -> 3

    //（3）访问数据
    for (elem <- map.keys) {
      // 使用get访问map集合的数据，会返回特殊类型Option(选项):有值（Some），无值(None)
      println(elem + "=" + map.get(elem).get)
    }

    //（4）如果key不存在，返回0
    println(map.get("d").getOrElse(0))
    println(map.getOrElse("d", 0))


  }
}
```

# 可变Map
1. 创建可变集合
2. 打印集合
3. 向集合增加数据
4. 删除数据
5. 修改数据

```scala
import scala.collection.mutable

object TestSet {

  def main(args: Array[String]): Unit = {

    //（1）创建可变集合
    val map = mutable.Map( "a"->1, "b"->2, "c"->3 )

    //（2）打印集合
    map.foreach((kv)=>{println(kv)})

    //（3）向集合增加数据
    map.+=("d"->4)

    // 将数值4添加到集合，并把集合中原值1返回
    val maybeInt: Option[Int] = map.put("a", 4)
    println(maybeInt.getOrElse(0))

    //（4）删除数据
    map.-=("b", "c")

    //（5）修改数据
    map.update("d",5)
    map("d") = 5
  }
}
```




