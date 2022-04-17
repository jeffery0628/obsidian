---
Create: 2022年 三月 20日, 星期日 11:13
tags: 
  - python
  - optparse
---



## 选项
选项类似字典，需提供名称和值。
选项相比于位置参数，有如下好处：
-   可以将它们设置为可选，当未提供选项时可以使用合理的默认值。
-   选项也可以接受与一个键相关的选项值，这可以提升可读性。
-   选项可以按照任意顺序提供。

### 选项类型
CLI脚本可以接受两种常见的选项类型。
-   一种是不需要或不接受带有值的选项。如 --verbose 和 --quit（通常分别以-v 和 -q的方式提供）
-   另一种类型是期望给参数赋一个值，而不仅仅把参数作为开关使用（如 --host、--port）。
    

### 向OptionParser添加选项
一旦有了OptionParser实例，就可以使用 add_option 方法向其添加选项。
```python
import optparse  
if __name__ == '__main__':  
	parser = optparse.OptionParser()  
  	parser.add_option('-q','--quiet',  
  						actionn='store_true',  
  						dest='quiet',  
  						help='Suppress output'  
  						)
```
这会为-q 和 --quiet 开关添加支持。在CLI脚本中，同时有长格式与短格式选项的版本非常常见。通过向add_option提供两个不同的字符串作为位置参数，add_option方法理解它们应该被接受并且它们互为别名。

指定 --quiet 表示的action关键字参数是一个标记，并没有期望传入一个变量。如果不设置action关键字参数，该选项就被假设期待一个值。设置action为store_true 或 store_false意味着不期待任何值，如果提供了标记，值就分别为True 或 False。

dest关键字参数用于确定在python中选项的名称。在options变量中选项的名称是quiet。在很多情况下，并不需要设置该参数。OptionParser基于选项的名称推断一个合适的名称。但是为了可读性和可维护性，显式的设置该参数是一个好主意。

help关键字用于为该选项设置帮助文本。当用户使用--help调用脚本时，显示help的选项内容。

### 带有值的选项
首先，考虑一个接受字符串的选项，可能被发送到数据库客户端的--host标记。该选项或许是可选的。数据库客户端的最大用例是连接到位于同一台机器上的数据库，因此localhost是一个合理的默认值。

```python
import optparse  
if __name__ == '__main__':  
	parser = optparse.OptionParser()  
  	parser.add_option('-H','--host',  
  						default='localhost',  
					  	dest='host',  
					  	type=str,  
					  	help='the host to connect to,Default to localhost')  
	options,arg = parser.parse_args()
```
如果不带参数调用该脚本，将看到应用默认值localhost，添加host选项，则重载默认值。如果没有提供选项，optparse则会报错
```bash
$python optparse_host.py  
The host is localhost  

$python optparse_host.py --host 0.0.0.0  
The host is 0.0.0.0  

$python optparse_host.py --host  
Usage:optparse-host.py [optionns]  
optparse_host.py:error:--host option requires an argument
```

### 非字符串值
```python
import optparse  

if __name__ == '__main__':  
	parser = optparse.OptionParser()  
  	parser.add_option('-H','--host',  
					  default='localhost',  
					  dest='host',  
					  type=str,  
					  help='the host to connect to,Default to localhost')  
	parser.add_option('-P','--port',  
					  default=3306,  
					  dest='port',  
					  type=int,  
					  help='the port to connect to,Default to 3306')  
	options,arg = parser.parse_args()  
	print('The host is %s, and the port is %d.' %(options.host,options.port))
```

### 指定选项值
#### 短格式语法

短格式语法是有一个连字符与一个单独字符的选项，比如 -q 、-H或-P。如果选项接受一个值（比如 -H 与-P），它必须写在经挨着选项之后。在选项与值之间的空格是可选的（-Hlocalhost 与 -H localhost）是等价的，并且可以选择将值包裹在双引号内（-Hlocalhost 与 -H“localhost”等价）。**但不能在短格式选项与值之间使用等号。**

#### 长格式语法

对于长格式语法（也就是--host 而不是 -H），所支持的排列略微不同。现在在选项与选项值之间必须有分隔符（与-Hlocalhost不同）。如果提供的是--hostlocalhost，解析器将永远不会知道选项结束语值开始的位置。分隔符可以是一个空格或一个等号（因此，--host=localhost 与 --host localhost等价）。

> 当编写CLI脚本时，应考虑既支持短格式语法，又支持长格式语法，尤其是对于将会被频繁使用的语法来说更要如此。
> 
> 当编写的CLI脚本需要提交到版本控制中，并在之后这些代码会被读取与维护，应考虑尽可能使用长格式语法。这使得CLI命令对于后续读取代码的人来说更容易靠直觉知道意思。

## 参数

实际上，任何没有附加到选项的参数都会被解析器认为是一个位置参数，并将其发送给由parser.parser_args()返回args变量。
```python
import optparse  
	
if __name__ == '__main__':  
	parser = optparse.OptionParser()  
  	options,args = parser.parse_args()  
  	print('The sum of the numbers  sent is : %d' %sum([int(i) for i in args]))
```

任何发送到该脚本的参数都是args变量的一部分，该脚本尝试将其转换为整型并相加。
```bash
$python optparse_sum.py 1 2 5  
The sum of the numbers  sent is 8
```
## 计数器

```python
import optparse  

if __name__ == '__main__':  
	parser = optparse.OptionParser()  
  	parser.add_option(  '-v',  
					  action='count',  
					  default=0,  
					  dest='verbosity',  
					  help='Be more verbose. This flag mah be repeated'  
  					)  

	options,args = parser.parse_args()  
	print('The verbosity level is : %d' %options.verbosity)
```
```bash
$python count_script.py  
The verbosity level is 0  
$python count_script.py -v  
The verbosity level is 1  
$python count_script.py -v -v  
The verbosity level is 2  
$python count_script.py -vvvvv  
The verbosity level is 5
```
## 列表值

```python
import optparse  

if __name__ == '__main__':  
	parser = optparse.OptionParser()  
  	parser.add_option(  '-u','--user',  
					  action='append',  
					  default=[],  
					  dest='users',  
					  help='The username to be printed'  
					  )  
  	options,args = parser.parse_args()  
  	for user in options.users:  
  	print("Username: %s." %user)  
```

```bash
$python echo_username.py  
:  
 
$python echo_username.py -u me  
Username:me.  

$python echo_username.py -u me -u myself  
Username:me.  
Username:myself

```


