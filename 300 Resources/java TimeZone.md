---
Create: 2021年 十月 28日, 星期四 09:34
tags: 
  - Engineering/java
  - 大数据
---



# java.util.TimeZone

通常，使用 TimeZone的`getDefault() `获取 TimeZone，getDefault 基于程序运行所在的时区创建 TimeZone。例如，对于在日本运行的程序，getDefault 基于日本标准时间创建 TimeZone 对象。也可以用TimeZone的 getTimeZone 及时区 ID 获取 TimeZone 。例如美国太平洋时区的时区 ID 是 "America/Los_Angeles"。因此，可以使用下面语句获得美国太平洋时间 TimeZone 对象： 

```java
TimeZone tz = TimeZone.getTimeZone("America/Los_Angeles");
```
