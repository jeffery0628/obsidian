---
Create: 2021年 十月 23日, 星期六 20:47
Linked Areas: 
Linked Project:
Linked Tools: [[500 Tools/510 Obsidian/511 plugins/templater]]
Other  Links: 
tags: 
  - Obsidian/plugins/templater

---


# 文件变量/函数

| 内部变量/函数                                                | 参数                                                         | 描述                                                         | 输出                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------- |
| `tp.file.content`                                            | 无                                                           | 检索文件内容                                                 |                               |
| `tp.file.create_new(template: TFile ⎮ string, filename?: string, open_new: boolean = false, folder?: TFolder)` | - `template`: 用于新文件内容的模板，或者字符串形式的文件内容。<br> - `filename`: 新文件的文件名，默认为`Untitled`.<br> - `open_new`:是否打开新创建的文件. <br> - `folder`: 新文件的保存路径 | 使用指定的模板创建一个新文件。                               | 无                            |
| `tp.file.creation_date(format: string = "YYYY-MM-DD HH:mm")` | - `format`: 日期格式                                         | 文件的创建时间                                               | `2021-01-06 20:27`            |
| `tp.file.cursor(order?: number)`                             | - `order`: 打开文件后当前光标指向哪里。                      | 插入模板后，将光标设置到指定位置。                           | 无                            |
| `tp.file.cursor_append(content: string)`                     | - `content`: 在光标之后追加的内容                            | 在文件光标之后追加一些内容                                   | 无                            |
| `tp.file.exists(filename: string)`                           | - `filename`: 文件名                                         | 检查文件是否存在，返回： `true` / `false` .                  | `true` or `false`             |
| `tp.file.find_tfile(filename: string)`                       | - `filename`: 要搜索的文件名称                               | 检索文件，并返回一个 [TFile](https://github.com/obsidianmd/obsidian-api/blob/ddd50214f530d23ee21f440a9fa64f4507176871/obsidian.d.ts#L2834) 对象. | `[object Object]`             |
| `tp.file.folder(relative: boolean = false)`                  | - `relative`: 如果设置为 `true`, 追加文件夹的相对路径.       | 检索文件夹路径.                                              | `Permanent Notes`             |
| `tp.file.include(include_link: string ⎮ TFile)`              | - `include_link`: 是否包含连接的文件.                        | 包括文件的链接内容。包含内容中的模板将被解析                 | `Header for all my templates` |
| `tp.file.last_modified_date(format: string = "YYYY-MM-DD HH:mm")` | - `format`: Format for the date, refer to [format reference](https://momentjs.com/docs/#/displaying/format/) | Retrieves the file's last modification date.                 | `2021-01-06 20:27`            |
| `tp.file.move(new_path: string)`                             | - `path`: 移动文件的目标路径。新路径需要包含文件夹名称以及文件名称，比如 `/Notes/MyNote` | 将文件移动到指定路径.                                        | 无                            |
| `tp.file.path(relative: boolean = false)`                    | - `relative` 是否是相对路径                                  | 获取文件在系统的的路径，                                     | `/path/to/file`               |
| `tp.file.rename(new_title: string)`                          | - `new_title`: 新文件名称                                    | 重命名文件                                                   | 无                            |
| `tp.file.selection()`                                        | 无                                                           | 检索文件内容.                                                | `Some selected text`          |
| `tp.file.tags`                                               | 无                                                           | 检索文件的tags                                               | `#note,#seedling,#obsidian`   |
| `tp.file.title`                                              | 无                                                           | 检索文件的标题                                               | `MyFile`                      |

```
File content: <% tp.file.content %>

File creation date: <% tp.file.creation_date() %>
File creation date with format: <% tp.file.creation_date("dddd Do MMMM YYYY HH:mm") %>

File creation: [[<% (await tp.file.create_new("MyFileContent", "MyFilename")).basename %>]]

File cursor: <% tp.file.cursor(1) %>

File cursor append: <% tp.file.cursor_append("Some text") %>
    
File existence: <% tp.file.exists("MyFile") %>

File find TFile: <% tp.file.find_tfile("MyFile").basename %>
    
File Folder: <% tp.file.folder() %>
File Folder with relative path: <% tp.file.folder(true) %>

File Include: <% tp.file.include("[[Template1]]") %>

File Last Modif Date: <% tp.file.last_modified_date() %>
File Last Modif Date with format: <% tp.file.last_modified_date("dddd Do MMMM YYYY HH:mm") %>

File Move: <% await tp.file.move("/A/B/" + tp.file.title) %>
File Move + Rename: <% await tp.file.move("/A/B/NewTitle") %>

File Path: <% tp.file.path() %>
File Path with relative path: <% tp.file.path(true) %>

File Rename: <% await tp.file.rename("MyNewName") %>
Append a "2": <% await tp.file.rename(tp.file.title + "2") %>

File Selection: <% tp.file.selection() %>

File tags: <% tp.file.tags %>

File title: <% tp.file.title %>
Strip the Zettelkasten ID of title (if space separated): <% tp.file.title.split(" ")[1] %>
```

