---
Create: 2021年 十月 27日, 星期三 13:45
tags: 
  - Engineering/java
  - 大数据
---





# Random类

此类的实例用于生成伪随机数。

```java
Random r = new Random(); 
int i = r.nextInt(n); // 返回一个伪随机数，范围在 0 （包括）和 指定值 n （不包括）之间的 int 值。

//1. 导包 
import java.util.Random; 
public class Demo01_Random {
    public static void main(String[] args) { 
        //2. 创建键盘录入数据的对象 
        Random r = new Random();
        for(int i = 0; i < 3; i++){ 
            //3. 随机生成一个数据 
            int number = r.nextInt(10); 
            //4. 输出数据 
            System.out.println("number:"+ number); 
        }
    }
}
```

> 创建一个 Random 对象，每次调用 `nextInt() `方法，都会生成一个随机数。

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]