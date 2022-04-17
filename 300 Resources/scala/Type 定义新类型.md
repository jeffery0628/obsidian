---
Create: 2022年 四月 10日, 星期日 21:28
tags: 
  - Engineering/scala
  - 大数据
---


使用type关键字可以定义新的数据数据类型名称，本质上就是类型的一个别名

```scala
object Test {

    def main(args: Array[String]): Unit = {
        
        type S=String
        var v:S="abc"
        def test():S="xyz"
    }
}
```





