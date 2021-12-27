---
Create: 2021年 十月 25日, 星期一 23:15
Linked Areas: 
Linked Project:
Linked Tools: 
Other  Links: 
tags: 
  - Engineering/java
  - 大数据
---
# Collection

`Collection` 是单列集合类的根接口，用于存储一系列符合某种规则的元素，它有两个重要的子接口，分别是`java.util.List`和`java.util.Set`。其中，`List`的特点是元素有序、元素可重复。`Set`的特点是元素无序，而且不可重复。`List`接口的主要实现类有`java.util.ArrayList`和`java.util.LinkedList`，`Set`接口的主要实现类有`java.util.HashSet`和`java.util.TreeSet`。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-collections/image-20200911215913962.png)

集合本身是一个工具，它存放在java.util包中。在`Collection`接口定义着单列集合框架中最最共性的内容。

## Collection 常用功能

Collection是所有单列集合的父接口，因此在Collection中定义了单列集合(List和Set)通用的一些方法，这些方法可用于操作所有的单列集合。方法如下：

-   `public boolean add(E e)`： 把给定的对象添加到当前集合中 。
-   `public void clear()` :清空集合中所有的元素。
-   `public boolean remove(E e)`: 把给定的对象在当前集合中删除。
-   `public boolean contains(E e)`: 判断当前集合中是否包含给定的对象。
-   `public boolean isEmpty()`: 判断当前集合是否为空。
-   `public int size()`: 返回集合中元素的个数。
-   `public Object[] toArray()`: 把集合中的元素，存储到数组中。
-   `public static <T> boolean addAll(Collection<T> c, T... elements)`:往集合中添加一些元素。
-   `public static void shuffle(List<?> list)`:打乱集合顺序。
-   `public static <T> void sort(List<T> list)`:将集合中元素按照默认规则排序。
-   `public static <T> void sort(List<T> list，Comparator<? super T> )`:将集合中元素按照指定规则排序。

## Comparator比较器

排序，简单的说就是两个对象之间比较大小，在JAVA中提供了两种比较实现的方式，一种是比较死板的，用`java.lang.Comparable`接口去实现，一种是灵活的当我需要做排序的时候在去选择的`java.util.Comparator`接口完成。

采用`public static <T> void sort(List<T> list)`这个方法完成排序，实际上要求了被排序的类型需要实现`Comparable`接口完成比较的功能，在String类型上如下：

 

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence
```

String类实现了这个接口，并完成了比较规则的定义，但是这样就把这种规则写死了，比如我想要字符串按照第一个字符降序排列，那么这样就要修改String的源代码，这是不可能的了。

那么这个时候我们可以使用`public static <T> void sort(List<T> list，Comparator<? super T> )`方法灵活的完成，这个里面就涉及到了`Comparator`这个接口，位于位于`java.util`包下，排序是`comparator`能实现的功能之一,该接口代表一个比较器，比较器具有可比性！顾名思义就是做排序的，通俗地讲需要比较两个对象谁排在前谁排在后，那么比较的方法就是比较其两个参数的顺序。

  `public int compare(String o1, String o2)`

> 两个对象比较的结果有三种：大于，等于，小于。
> 
> 如果要按照升序排序， 则o1 小于o2，返回（负数），相等返回0，01大于02返回（正数） 如果要按照降序排序 则o1 小于o2，返回（正数），相等返回0，01大于02返回（负数）

```java
public class CollectionsDemo3 {
    public static void main(String[] args) {
        ArrayList<String> list = new ArrayList<String>();
        list.add("cba");
        list.add("aba");
        list.add("sba");
        list.add("nba");
        //排序方法  按照第一个单词的降序
        Collections.sort(list, new Comparator<String>() {
            @Override
            public int compare(String o1, String o2) {
                return o2.charAt(0) - o1.charAt(0);
            }
        });
        System.out.println(list);
    }
}
```



## Comparable和Comparator

`Comparable`：强行对实现它的每个`类`的对象进行整体排序。这种排序被称为类的自然排序，类的`compareTo()`方法被称为它的自然比较方法。只能在类中实现`compareTo()`一次，不能经常修改类的代码实现自己想要的排序。实现此接口的对象列表（和数组）可以通过`Collections.sort`（和`Arrays.sort`）进行自动排序，对象可以用作有序映射中的键或有序集合中的元素，无需指定比较器。

```java
public class Student implements Comparable<Student>{
    ....
    @Override
    public int compareTo(Student o) {
        return this.age-o.age;//升序
    }
}
```

`Comparator`强行对某个对象进行整体排序。可以将`Comparator` 传递给sort方法（如Collections.sort或 Arrays.sort），从而允许在排序顺序上实现精确控制。还可以使用Comparator来控制某些数据结构（如有序set或有序映射）的顺序，或者为那些没有自然顺序的对象collection提供排序。

```java
Collections.sort(list, new Comparator<Student>() {
            @Override
            public int compare(Student o1, Student o2) {
                // 年龄降序
                int result = o2.getAge()-o1.getAge();//年龄降序

                if(result==0){//第一个规则判断完了 下一个规则 姓名的首字母 升序
                    result = o1.getName().charAt(0)-o2.getName().charAt(0);
                }

                return result;
            }
        });
```

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]