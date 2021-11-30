---
Create: 2021年 十月 23日, 星期六 20:28
Linked Areas: 
Linked Project:
Linked Tools: [[500 Tools/510 Obsidian/511 plugins/templater]]
Other  Links: 
tags: 
  - Obsidian/plugins/templater

---
# 日期变量/函数

| 内部变量/函数                                                | 参数                                                         | 描述           | 输出样式     |
| ------------------------------------------------------------ | ------------------------------------------------------------ | -------------- | ------------ |
| `tp.date.now(format: string = "YYYY-MM-DD", offset?: number⎮string, reference?: string, reference_format?: string)` | - `format`: 格式化日期显示样式，参考 [format reference](https://momentjs.com/docs/#/displaying/format/) <br>- `offset`:表示与当天日期的偏移量, 比如设置为-7表示上周日期 。<br>- `reference`:设置参照物<br> - `reference_format`:参照物日期的显示样式. | 获取日期       | `2021-10-23` |
| `tp.date.tomorrow(format: string = "YYYY-MM-DD")`            | - `format`: 格式化显示日期样式                               | 获取明天日期   | `2020-10-24` |
| `tp.date.weekday(format: string = "YYYY-MM-DD", weekday: number, reference?: string, reference_format?: string)` | - `format`: 格式化显示日期样式<br> - `weekday`: 本周的第几天，`0`表示一周的第一天，即周一, `-7` 表示上周<br> - `reference`: 参照物<br> - `reference_format`: 参照物的日期表示形式 | 获取周x的日期  | `2021-04-06` |
| `tp.date.yesterday(format: string = "YYYY-MM-DD")`           | - `format`: 日期的表示形式                                   | 获取昨天的日期 | `2020-11-07` |







```
Date now: <% tp.date.now() %>
Date now with format: <% tp.date.now("Do MMMM YYYY") %>

Last week: <% tp.date.now("dddd Do MMMM YYYY", -7) %>
Today: <% tp.date.now("dddd Do MMMM YYYY, ddd") %>
Next week: <% tp.date.now("dddd Do MMMM YYYY", 7) %>

Last month: <% tp.date.now("YYYY-MM-DD", "P-1M") %>
Next year: <% tp.date.now("YYYY-MM-DD", "P1Y") %>

File's title date + 1 day (tomorrow): <% tp.date.now("YYYY-MM-DD", 1, tp.file.title, "YYYY-MM-DD") %>
File's title date - 1 day (yesterday): <% tp.date.now("YYYY-MM-DD", -1, tp.file.title, "YYYY-MM-DD") %>

Date tomorrow with format: <% tp.date.tomorrow("Do MMMM YYYY") %>    

This week's monday: <% tp.date.weekday("YYYY-MM-DD", 0) %>
Next monday: <% tp.date.weekday("YYYY-MM-DD", 7) %>
File's title monday: <% tp.date.weekday("YYYY-MM-DD", 0, tp.file.title, "YYYY-MM-DD") %>
File's title next monday: <% tp.date.weekday("YYYY-MM-DD", 7, tp.file.title, "YYYY-MM-DD") %>

Date yesterday with format: <% tp.date.yesterday("Do MMMM YYYY") %>
```



