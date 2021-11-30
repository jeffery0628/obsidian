---
Create: 2021年 十月 24日, 星期日 12:36
Linked Areas: 
Linked Project:
Linked Tools: [[500 Tools/510 Obsidian/511 plugins/templater]]
Other  Links: 
tags: 
  - Obsidian/plugins/templater


---

# WEB变量/函数

| 内部变量/函数                                          | 参数                                                         | 描述                                     | 输出示例                                                     |
| ------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------------- | ------------------------------------------------------------ |
| `tp.web.daily_quote()`                                 | 无                                                           | 随机获取一句名言 https://quotes.rest/    | > Climb the mountains and get their good tidings. Nature's peace will flow into you as sunshine flows into trees. The winds will blow their own freshness into you, and the storms their energy, while cares will drop away from you like the leaves of Autumn.<br/>> &mdash; <cite>John Muir</cite> |
| `tp.web.random_picture(size?: string, query?: string)` | - `size`: 图片大小<br>- `query`:图片的主题，如果是多个主题，用逗号隔开 `,` | 从 https://unsplash.com/随机获取一张图片 |                                                              |

```
Web Daily quote:  
<% tp.web.daily_quote() %>

Web Random picture: 
<% tp.web.random_picture() %>

Web Random picture with size: 
<% tp.web.random_picture("200x200") %>

Web random picture with size + query: 
<% tp.web.random_picture("200x200", "landscape,water") %>
```

