---
Create: 2021年 十月 30日, 星期六 14:35
tags: 
  - Engineering/java
  - 大数据
---
## Supplier 接口

`java.util.function.Supplier<T> `接口仅包含一个无参的方法：` T get() `。用来获取一个泛型参数指定类型的对象数据。由于这是一个函数式接口，这也就意味着对应的Lambda表达式需要“对外提供”一个符合泛型类型的对象数据。

```java
import java.util.function.Supplier;

public class Demo08Supplier {
    private static String getString(Supplier<String> function) { 
        return function.get(); 
    }
    
    public static void main(String[] args) { 
        String msgA = "Hello"; 
        String msgB = "World"; 
        System.out.println(getString(() ‐> msgA + msgB)); 
    }
}
```

例1：使用 Supplier 接口作为方法参数类型，通过Lambda表达式求出int数组中的最大值。

> 接口的泛型请使用 java.lang.Integer 类。

```java
public class Demo02Test {
    //定一个方法,方法的参数传递Supplier,泛型使用Integer 
    public static int getMax(Supplier<Integer> sup){ 
        return sup.get(); 
    }
    
    public static void main(String[] args) { 
        int arr[] = {2,3,4,52,333,23};
        //调用getMax方法,参数传递Lambda 
        int maxNum = getMax(()‐>{ 
            //计算数组的最大值 
            int max = arr[0]; 
            for(int i : arr){ 
                if(i>max){ 
                    max = i; 
                } 
            } 
            return max; 
        }); 
        System.out.println(maxNum);
    }
}
```




[[200 Areas/230 Engineering/231 java|java 目录]]