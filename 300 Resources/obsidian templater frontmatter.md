---
Create: 2021年 十月 24日, 星期日 11:29
Linked Areas: 
Linked Project:
Linked Tools: [[500 Tools/510 Obsidian/511 plugins/templater]]
Other  Links: 
tags: 
  - Obsidian/plugins/templater

---

# 文件metadata信息

| 内部变量/函数                                | 参数 | 描述                     | 输出示例 |
| -------------------------------------------- | ---- | ------------------------ | -------- |
| `tp.frontmatter.<frontmatter_variable_name>` | 无   | 获取文件metadata中的信息 | `value`  |



```
---
alias: myfile
note type: seedling
---

file content
```



```
File's metadata alias: <% tp.frontmatter.alias %>
Note's type: <% tp.frontmatter["note type"] %>
```

