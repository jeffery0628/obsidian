---
Create: 2021年 十月 28日, 星期四 13:21
tags: 
  - Engineering/java
  - 大数据
---
# File类

`java.io.File` 类是文件和目录路径名的抽象表示，主要用于文件和目录的创建、查找和删除等操作。

## 构造方法

* `public File(String pathname) ` ：通过将给定的**路径名字符串**转换为抽象路径名来创建新的 File实例。  
* `public File(String parent, String child) ` ：从**父路径名字符串和子路径名字符串**创建新的 File实例。
* `public File(File parent, String child)` ：从**父抽象路径名和子路径名字符串**创建新的 File实例。  

构造举例，代码如下：

```java
// 文件路径名
String pathname = "/home/aaa.txt";
File file1 = new File(pathname); 

// 文件路径名
String pathname2 = "/home/aaa/bbb.txt";
File file2 = new File(pathname2); 

// 通过父路径和子路径字符串
 String parent = "/home/aaa/";
 String child = "bbb.txt";
 File file3 = new File(parent, child);

// 通过父级File对象和子路径字符串
File parentDir = new File("/home/aaa");
String child = "bbb.txt";
File file4 = new File(parentDir, child);
```

> 1. 一个File对象代表硬盘中实际存在的一个文件或者目录。
> 2. 无论该路径下是否存在文件或者目录，都不影响File对象的创建。

## 常用方法

### 获取功能的方法

* `public String getAbsolutePath() ` ：返回此File的绝对路径名字符串。
* ` public String getPath() ` ：将此File转换为路径名字符串。 
* `public String getName()`  ：返回由此File表示的文件或目录的名称。  
* `public long length()`  ：返回由此File表示的文件的长度。 

方法演示，代码如下：

```java
public class FileGet {
    public static void main(String[] args) {
        File f = new File("d:/aaa/bbb.java");     
        System.out.println("文件绝对路径:"+f.getAbsolutePath());//文件绝对路径:d:\aaa\bbb.java
        System.out.println("文件构造路径:"+f.getPath());//文件构造路径:d:\aaa\bbb.java
        System.out.println("文件名称:"+f.getName());//文件名称:bbb.java
        System.out.println("文件长度:"+f.length()+"字节");//文件长度:636字节

        File f2 = new File("d:/aaa");      
        System.out.println("目录绝对路径:"+f2.getAbsolutePath()); //目录绝对路径:d:\aaa
        System.out.println("目录构造路径:"+f2.getPath());//目录构造路径:d:\aaa
        System.out.println("目录名称:"+f2.getName()); //目录名称:aaa
        System.out.println("目录长度:"+f2.length()); //目录长度:4096
    }
}
```

> API中说明：`length()`，表示文件的长度。但是File对象表示目录，则返回值未指定。

### 绝对路径和相对路径

* **绝对路径**：从盘符开始的路径，这是一个完整的路径。
* **相对路径**：相对于项目目录的路径，这是一个便捷的路径，开发中经常使用。

```java
public class FilePath {
    public static void main(String[] args) {
      	// D盘下的bbb.java文件
        File f = new File("D:\\bbb.java"); //D:\bbb.java
        System.out.println(f.getAbsolutePath());
      	
		// 项目下的bbb.java文件
        File f2 = new File("bbb.java");
        System.out.println(f2.getAbsolutePath()); //D:\idea_project_test4\bbb.java
    }
}
```

### 判断功能的方法

- `public boolean exists()` ：此File表示的文件或目录是否实际存在。
- `public boolean isDirectory()` ：此File表示的是否为目录。
- `public boolean isFile()` ：此File表示的是否为文件。


方法演示，代码如下：

```java
public class FileIs {
    public static void main(String[] args) {
        File f = new File("d:\\aaa\\bbb.java");
        File f2 = new File("d:\\aaa");
      	// 判断是否存在
        System.out.println("d:\\aaa\\bbb.java 是否存在:"+f.exists()); //d:\aaa\bbb.java 是否存在:true
        System.out.println("d:\\aaa 是否存在:"+f2.exists()); //d:\aaa 是否存在:true
      	// 判断是文件还是目录
        System.out.println("d:\\aaa 文件?:"+f2.isFile()); //d:\aaa 文件?:false
        System.out.println("d:\\aaa 目录?:"+f2.isDirectory()); //d:\aaa 目录?:true
    }
}
```

### 创建删除功能的方法

- `public boolean createNewFile()` ：当且仅当具有该名称的文件尚不存在时，创建一个新的空文件。 
- `public boolean delete()` ：删除由此File表示的文件或目录。  
- `public boolean mkdir()` ：创建由此File表示的目录。
- `public boolean mkdirs()` ：创建由此File表示的目录，包括任何必需但不存在的父目录。

方法演示，代码如下：

```java
public class FileCreateDelete {
    public static void main(String[] args) throws IOException {
        // 文件的创建
        File f = new File("aaa.txt");
        System.out.println("是否存在:"+f.exists()); // false
        System.out.println("是否创建:"+f.createNewFile()); // true
        System.out.println("是否存在:"+f.exists()); // true
		
     	// 目录的创建
      	File f2= new File("newDir");	
        System.out.println("是否存在:"+f2.exists());// false
        System.out.println("是否创建:"+f2.mkdir());	// true
        System.out.println("是否存在:"+f2.exists());// true

		// 创建多级目录
      	File f3= new File("newDira\\newDirb");
        System.out.println(f3.mkdir());// false
        File f4= new File("newDira\\newDirb");
        System.out.println(f4.mkdirs());// true
      
      	// 文件的删除
       	System.out.println(f.delete());// true
      
      	// 目录的删除
        System.out.println(f2.delete());// true
        System.out.println(f4.delete());// false
    }
}
```

> API中说明：`delete`方法，如果此File表示目录，则目录必须为空才能删除。

## 目录的遍历

* `public String[] list()` ：返回一个String数组，表示该File目录中的所有子文件或目录。


* `public File[] listFiles()` ：返回一个File数组，表示该File目录中的所有的子文件或目录。  

```java
public class FileFor {
    public static void main(String[] args) {
        File dir = new File("d:\\java_code");
      
      	//获取当前目录下的文件以及文件夹的名称。
		String[] names = dir.list();
		for(String name : names){
			System.out.println(name);
		}
        //获取当前目录下的文件以及文件夹对象，只要拿到了文件对象，那么就可以获取更多信息
        File[] files = dir.listFiles();
        for (File file : files) {
            System.out.println(file);
        }
    }
}
```

> 调用listFiles方法的File对象，表示的必须是`实际存在的目录`，否则返回null，无法进行遍历。

### 文件搜索	

需求：搜索`D:\aaa` 目录中的`.java` 文件。

**分析**：

1. 目录搜索，无法判断多少级目录，所以使用递归，遍历所有目录。
2. 遍历目录时，获取的子文件，通过文件名称，判断是否符合条件。

```java
public class DiGuiDemo3 {
    public static void main(String[] args) {
        // 创建File对象
        File dir  = new File("D:\\aaa");
      	// 调用打印目录方法
        printDir(dir);
    }

    public static void printDir(File dir) {
      	// 获取子文件和目录
        File[] files = dir.listFiles();
      	
      	// 循环打印
        for (File file : files) {
            if (file.isFile()) {
              	// 是文件，判断文件名并输出文件绝对路径
                if (file.getName().endsWith(".java")) {
                    System.out.println("文件名:" + file.getAbsolutePath());
                }
            } else {
                // 是目录，继续遍历,形成递归
                printDir(file);
            }
        }
    }
}
```

### 文件过滤器优化

`java.io.FileFilter`是一个接口，是File的过滤器。 该接口的对象可以传递给File类的`listFiles(FileFilter)` 作为参数， 接口中只有一个方法。`boolean accept(File pathname)  ` ：测试pathname是否应该包含在当前File目录中，符合则返回true。

**分析**：

1. 接口作为参数，需要传递子类对象，重写其中方法。我们选择匿名内部类方式，比较简单。
2. `accept`方法，参数为File，表示当前File下所有的子文件和子目录。保留住则返回true，过滤掉则返回false。保留规则：
	1. 要么是.java文件。
	2. 要么是目录，用于继续遍历。
3. 通过过滤器的作用，`listFiles(FileFilter)`返回的数组元素中，子文件对象都是符合条件的，可以直接打印。

```java
public class DiGuiDemo4 {
    public static void main(String[] args) {
        File dir = new File("D:\\aaa");
        printDir2(dir);
    }
  
    public static void printDir2(File dir) {
      	// 匿名内部类方式,创建过滤器子类对象
        File[] files = dir.listFiles(new FileFilter() {
            @Override
            public boolean accept(File pathname) {
                return pathname.getName().endsWith(".java")||pathname.isDirectory();
            }
        });
      	// 循环打印
        for (File file : files) {
            if (file.isFile()) {
                System.out.println("文件名:" + file.getAbsolutePath());
            } else {
                printDir2(file);
            }
        }
    }
}      
```

### Lambda优化

`FileFilter`是只有一个方法的接口，因此可以用lambda表达式简写。lambda格式：

```java
public static void printDir3(File dir) {
  	// lambda的改写
    File[] files = dir.listFiles(f ->{return f.getName().endsWith(".java") || f.isDirectory(); });
  	
	// 循环打印
    for (File file : files) {
        if (file.isFile()) {
            System.out.println("文件名:" + file.getAbsolutePath());
      	} else {
        	printDir3(file);
      	}
    }
}
```





[[200 Areas/230 Engineering/232 大数据/java|java 目录]]