---
Create: 2021年 十月 27日, 星期三 22:46
tags: 
  - Engineering/java
  - 大数据
---

# Scanner

Scanner 是一个可以解析基本类型和字符串的简单文本扫描器。

```java
public static void main(String[] args) {
    Scanner sc = new Scanner(System.in); // 构造一个新的 Scanner ，它生成的值是从指定的输入流扫描的。
    int i = sc.nextInt(); // 将输入信息的下一个标记扫描为一个 int 值。
    
    // or
    int j = new Scanner(System.in).nextInt();
}
```

> System.in 系统输入指的是通过键盘录入数据。

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]