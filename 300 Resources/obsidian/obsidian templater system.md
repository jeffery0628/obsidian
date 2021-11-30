
---
Create: 2021年 十月 24日, 星期日 11:41
Linked Areas: 
Linked Project:
Linked Tools: [[500 Tools/510 Obsidian/511 plugins/templater]]
Other  Links: 
tags: 
  - Obsidian/plugins/templater

---


# 系统参数/变量

| 内部变量/函数                                                | 参数                                                         | 描述                               | 输出示例                 |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------------------------- | ------------------------ |
| `tp.system.clipboard()`                                      | 无                                                           | 检索粘贴板上是内容                 | `This is my copied text` |
| `tp.system.prompt(prompt_text?: string, default_value?: string, throw_on_cancel: boolean = false)` | - `prompt_text`: 输入文本的属性类型<br> - `default_value`: 输入默认值<br> - `throw_on_cancel`: 如果prompt被取消，是否报错。 | 生成一个提示模式并返回用户的输入。 | `A value I entered`      |
| `tp.system.suggester(text_items: string[] ⎮ ((item: T) => string), items: T[], throw_on_cancel: boolean = false, placeholder: string = "")` | - `text_items`: 提示的文本内容<br>- `items`: 对应顺序的值<br>. - `throw_on_cancel`: 当prompt取消后，抛出异常。<br> - `placeholder`: 占位符. | 生成建议提示并返回用户选择的项目。 | `A value I chose`        |





```
Clipboard content: <% tp.system.clipboard() %>

Entered value: <% tp.system.prompt("Please enter a value") %>
Mood today: <% tp.system.prompt("What is your mood today ?", "happy") %>

Mood today: <% tp.system.suggester(["Happy", "Sad", "Confused"], ["Happy", "Sad", "Confused"]) %>
Picked file: [[<% (await tp.system.suggester((item) => item.basename, app.vault.getMarkdownFiles())).basename %>]]
```

