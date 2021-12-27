---
Create: 2021年 十月 28日, 星期四 09:37
tags: 
  - Engineering/java
  - 大数据
---

# java.utils.Locale

Locale 对象表示了特定的地理、政治和文化地区。需要 Locale 来执行其任务的操作称为语言环境敏感的操作，它使用 Locale 为用户量身定制信息。例如，显示一个数值，日期就是语言环境敏感的操作，应该根据用户的国家、地区或文化的风俗/传统来格式化该数值。获取Locale对象：

```java
public static void main(String[] args) {
		Locale loc = Locale.CHINA;
		System.out.println(loc);//zh_CN
		System.out.println(Locale.US);//en_US
		System.out.println(Locale.JAPAN);//ja_JP
		
		Locale c = new Locale("zh","CN");
		System.out.println(c);
}
```

[[200 Areas/230 Engineering/232 大数据/java|java 目录]]