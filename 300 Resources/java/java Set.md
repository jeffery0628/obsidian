---
Create: 2021年 十月 25日, 星期一 23:33
Linked Areas: 
Linked Project:
Linked Tools: 
Other  Links: 
tags: 
  - Engineering/java
  - 大数据
---

# Set

`java.util.Set`接口和`java.util.List`接口一样，同样继承自`Collection`接口，它与`Collection`接口中的方法基本一致，并没有对`Collection`接口进行功能上的扩充，只是比`Collection`接口更加严格了。与`List`接口不同的是，`Set`接口中元素无序，并且都会以某种规则保证存入的元素不出现重复。

`Set`集合有多个子类，这里介绍其中的`java.util.HashSet`、`java.util.LinkedHashSet`这两个集合。

> Set集合取出元素的方式可以采用：迭代器、foreach。

## HashSet

`java.util.HashSet`是`Set`接口的一个实现类，它所存储的元素是不可重复的，并且元素都是无序的(即存取顺序不一致)。`java.util.HashSet`底层的实现其实是一个`java.util.HashMap`支持。

`HashSet`是根据对象的哈希值来确定元素在集合中的存储位置，因此具有良好的存取和查找性能。保证元素唯一性的方式依赖于：`hashCode`与`equals`方法。

### HashSet集合存储数据的结构（哈希表）

哈希表：在**JDK1.8**之前，哈希表底层采用数组+链表实现，即使用链表处理冲突，同一hash值的链表都存储在一个链表里。但是当位于一个桶中的元素较多，即hash值相等的元素较多时，通过key值依次查找的效率较低。而JDK1.8中，哈希表存储采用数组+链表+红黑树实现，当链表长度超过阈值（8）时，将链表转换为红黑树，这样大大减少了查找时间。

简单的来说，哈希表是由数组+链表+红黑树（JDK1.8增加了红黑树部分）实现的，如下图所示：
![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-collections/%E5%93%88%E5%B8%8C%E8%A1%A8.png)

HashSet的一个存储流程图来说明HashSet的存储原理：

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-collections/%E5%93%88%E5%B8%8C%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

总而言之，**JDK1.8**引入红黑树大程度优化了HashMap的性能，那么对于我们来讲保证HashSet集合元素的唯一，其实就是根据对象的`hashCode`和`equals`方法来决定的。如果我们往集合中存放自定义的对象，那么保证其唯一，就必须复写`hashCode`和`equals`方法建立属于当前对象的比较方式。

## HashSet存储自定义类型元素

给HashSet中存放自定义类型元素时，需要重写对象中的`hashCode`和`equals`方法，建立自己的比较方式，才能保证HashSet集合中的对象唯一

创建自定义Student类

~~~java
public class Student {
 	
    ...

    @Override
    public boolean equals(Object o) {
        if (this == o)
            return true;
        if (o == null || getClass() != o.getClass())
            return false;
        Student student = (Student) o;
        return age == student.age &&
               Objects.equals(name, student.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age);
    }
}
~~~

~~~java
public class HashSetDemo2 {
    public static void main(String[] args) {
        //创建集合对象   该集合中存储 Student类型对象
        HashSet<Student> stuSet = new HashSet<Student>();
        //存储 
        stuSet.add(new Student("于谦", 43););
        stuSet.add(new Student("郭德纲", 44));
        stuSet.add(new Student("于谦", 43));
        stuSet.add(new Student("郭麒麟", 23));
        stuSet.add(stu);

        for (Student stu2 : stuSet) {
            System.out.println(stu2);
        }
    }
}
执行结果：
Student [name=郭德纲, age=44]
Student [name=于谦, age=43]
Student [name=郭麒麟, age=23]
~~~

## LinkedHashSet

HashSet保证元素唯一，可是元素存放进去是没有顺序的，`java.util.LinkedHashSet`可以保证有序,它是链表和哈希表组合的一个数据存储结构。



# Iterator

在程序开发中，经常需要遍历集合中的所有元素。针对这种需求，JDK专门提供了一个接口。`java.util.Iterator`。`Iterator`接口也是Java集合中的一员，但它与`Collection`、`Map`接口有所不同，`Collection`接口与`Map`接口主要用于存储元素，而`Iterator`主要用于迭代访问（即遍历）`Collection`中的元素，因此`Iterator`对象也被称为迭代器。

想要遍历Collection集合，那么就要获取该集合迭代器完成迭代操作：

`public Iterator iterator()`: 获取集合对应的迭代器，用来遍历集合中的元素的。

Iterator接口的常用方法如下：

* `public E next()`:返回迭代的下一个元素。
* `public boolean hasNext()`:如果仍有元素可以迭代，则返回 true。

~~~java
public class IteratorDemo {
  	public static void main(String[] args) {
        // 使用多态方式 创建对象
        Collection<String> coll = new ArrayList<String>();

        // 添加元素到集合
        coll.add("串串星人");
        coll.add("吐槽星人");
        coll.add("汪星人");
        //遍历
        //使用迭代器 遍历   每个集合对象都有自己的迭代器
        Iterator<String> it = coll.iterator();
        //  泛型指的是 迭代出 元素的数据类型
        while(it.hasNext()){ //判断是否有迭代元素
            String s = it.next();//获取迭代出的元素
            System.out.println(s);
        }
  	}
}
~~~

> 在进行集合元素取出时，如果集合中已经没有元素了，还继续使用迭代器的next方法，将会发生java.util.NoSuchElementException没有集合元素的错误。

## 迭代器的实现原理

当遍历集合时，首先通过调用集合的`iterator()`方法获得迭代器对象，然后使用`hashNext()`方法判断集合中是否存在下一个元素，如果存在，则调用`next()`方法将元素取出，否则说明已到达了集合末尾，停止遍历元素。

`Iterator`迭代器对象在遍历集合时，内部采用指针的方式来跟踪集合中的元素。

![](https://images-1257755739.cos.ap-guangzhou.myqcloud.com/hexo/posts/java-collections/02_%E8%BF%AD%E4%BB%A3%E5%99%A8%E7%9A%84%E5%AE%9E%E7%8E%B0%E5%8E%9F%E7%90%86-1-.bmp)

在调用Iterator的next方法之前，迭代器的索引位于第一个元素之前，不指向任何元素，当第一次调用迭代器的next方法后，迭代器的索引会向后移动一位，指向第一个元素并将该元素返回，当再次调用next方法时，迭代器的索引会指向第二个元素并将该元素返回，依此类推，直到hasNext方法返回false，表示到达了集合的末尾，终止对元素的遍历。

## foreach

`foreach循环`：是**JDK1.5**以后出来的一个高级for循环，专门用来遍历数组和集合的。它的内部原理其实是个Iterator迭代器，所以在遍历的过程中，不能对集合中的元素进行增删操作。

~~~java
for(元素的数据类型  变量 : Collection集合or数组){ 
  	//写操作代码
}
~~~

> foreach用于遍历Collection和数组。通常只进行遍历元素，不要在遍历的过程中对集合元素进行增删操作。

### 遍历数组

~~~java
public class NBForDemo1 {
    public static void main(String[] args) {
		int[] arr = {3,5,6,87};
       	//使用增强for遍历数组
		for(int a : arr){//a代表数组中的每个元素
			System.out.println(a);
		}
	}
}
~~~

### 遍历集合

~~~java
public class NBFor {
    public static void main(String[] args) {        
    	Collection<String> coll = new ArrayList<String>();
    	coll.add("小河神");
    	coll.add("老河神");
    	coll.add("神婆");
    	//使用增强for遍历
    	for(String s :coll){//接收变量s代表 代表被遍历到的集合元素
    		System.out.println(s);
    	}
	}
}
~~~

> 新for循环必须有被遍历的目标。目标只能是Collection或者是数组。新式for仅仅作为遍历操作出现。


[[200 Areas/230 Engineering/232 大数据/java|java 目录]]