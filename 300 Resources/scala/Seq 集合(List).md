---
Create: 2022年 四月 10日, 星期日 21:54
tags: 
  - Engineering/scala
  - 大数据
---
## 不可变List
1. List默认为不可变集合
2. 创建一个List（数据有顺序，可重复）
3. 遍历List
4. List增加数据
5. 集合间合并：将一个整体拆成一个一个的个体，称为扁平化
6. 取指定数据
7. 空集合Nil

```scala
object TestList {

  def main(args: Array[String]): Unit = {

    //（1）List默认为不可变集合
    //（2）创建一个List（数据有顺序，可重复）
    val list: List[Int] = List(1,2,3,4,3)
    // (3) 遍历List
    println(list.mkString(","))  //  1,2,3,4,3

    //（4）List增加数据
    //（4.1）::的运算规则从右向左
    val list1 = 7::6::5::list
    println(list1.mkString(","))  //  7,6,5,1,2,3,4,3
    //（4.2）添加到第一个元素位置
    val list2 = list.+:(5)
    println(list2.mkString(",")) // 5,1,2,3,4,3

    //（5）集合间合并：将一个整体拆成一个一个的个体，称为扁平化
    val list3 = List(8,9)
    val list4 = list3::list1
    println(list4.mkString(",")) //  List(8, 9),7,6,5,1,2,3,4,3
    val list5 = list3:::list1
    println(list5.mkString(","))  // 8,9,7,6,5,1,2,3,4,3

    //（6）取指定数据
    println(list(0))

    //（7）空集合Nil
    val list6 = 1::2::3::4::Nil
    println(list6.mkString(","))  // 1,2,3,4


  }
}
```



## 可变ListBuffer

1. 创建一个可变集合ListBuffer
2. 向集合中添加数据
3. 打印集合数据

```scala
import scala.collection.mutable.ListBuffer

object TestList {

  def main(args: Array[String]): Unit = {

    //（1）创建一个可变集合
    val buffer = ListBuffer(1,2,3,4)

    //（2）向集合中添加数据
    buffer.+=(5)
    println(buffer.mkString(","))   // 1,2,3,4,5
    buffer.append(6)
    println(buffer.mkString(","))  //  1,2,3,4,5,6
    buffer.insert(1,2)
    println(buffer.mkString(","))  //  1,2,2,3,4,5,6

    //（3）打印集合数据
    buffer.foreach(println)

    //（4）修改数据
    buffer(1) = 6
    println(buffer.mkString(",")) // 1,6,2,3,4,5,6
    buffer.update(1,7)
    println(buffer.mkString(",")) // 1,7,2,3,4,5,6

    //（5）删除数据
    val newBuffer = buffer.-(5)    // 返回一个新ListBuffer，原来的不变
    println(newBuffer.mkString(","))  // 1,7,2,3,4,6
    println(buffer.mkString(","))     // 1,7,2,3,4,5,6
    buffer.-=(5)  // 返回一个新ListBuffer将原ListBuffer覆盖
    println(buffer.mkString(","))     // 1,7,2,3,4,6
    buffer.remove(5)  // 删除第五个元素，返回一个新ListBuffer
    println(buffer.mkString(",")) // 1,7,2,3,4
  }
}
```






