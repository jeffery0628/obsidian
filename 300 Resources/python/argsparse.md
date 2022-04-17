---
Create: 2022年 三月 20日, 星期日 11:31
tags: 
  - python
  - argsparse
---

[官方文档](https://docs.python.org/zh-cn/3/library/argparse.html#module-argparse)

python 提供的第二个用于解析CLI参数与选项的库称为argparse。argparse模块被认为是optparse的继任者。在很多方面，argparse在概念上都类似于optparse，基本原则是相同的。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    args = parser.parse_args()
    print('The script ran successfully and did nothing')
    
```

parse_args不像optparse返回一个二元组，而是返回一个同时包含位置参数与选项的对象。



## 选项


### 选项标记

第一类选项是标记，比如用于相似模式的 -v 或 --verbose，或者用于阻止大部分或所有输出结果的-q 或 -quit。这些选项不期望有值。选项是否存在决定了在解析器中对应的布尔值。

```python
parser.add_argument('-q','--quiet',
					action='store_true',
					dest='quiet',
					help='Supress output'
)
```

首先，注意action变量，该变量被设置为store_true，这就是为什么解析器并不期望有值的原因。大多数选项并不需要指定action的值（默认值为store，用于存储它接受的值）。指定store_true或store_false是表示一个选项时标记且并不该接受任何值的最常见方式。

### 替代前缀

大多数CLI脚本使用连字符（-）作为选项前缀，但有些脚本可能使用不同的字符。例如，一段只打算在windows环境执行的脚本可能倾向使用/字符，这会与很多windows命令行程序保持一致。

可以通过为ArgumentParser构造函数提供prefix_chars关键字参数来改变作为前缀使用的字符，如下所示：

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser(prefix_chars='/')
    parser.add_argument('/q','//quiet',
                        action='store_true',
                        dest='quiet',
                        help='Supress output'
                        )
    args = parser.parse_args()
    
    print('Quit mode is %r'%args.quiet)
```

```bash
$python argparse_quiet.py 
Quit mode is False
$pythonn argparse_quiet.py /q
Quit mode is True
```



### 带有值的选项

接受值的选项在本质上是类似的。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-H','--host',
                        default='localhost',
                        dest='host',
                        help='The host to connect to.Defaults to localhost',
                        type=str
                        )
    args = parser.parse_args()
    
    print('The host is %s'%args.host)
```

与optparse十分相似，关键字参数相同，并完成同样的任务。

参数type控制最终期望所属的Python类型。该值常见的是int或float，还有少量其他类型也同样可用。

使用argparse对参数解析，无论是使用短格式还是长格式语法，都可以使用一个空格或等于号来分隔选项和值。

```bash
$python argparse_args.py -Hlocalhost
The host is localhost
$python argparse_args.py -H"localhost"
The host is localhost
$python argparse_args.py -H=localhost
The host is localhost
$python argparse_args.py -H="localhost"
The host is localhost
$python argparse_args.py -H localhost
The host is localhost
$python argparse_args.py -H "localhost"
The host is localhost
$python argparse_args.py --host=localhost
The host is localhost
$python argparse_args.py --host="localhost"
The host is localhost
$python argparse_args.py --host localhost
The host is localhost
$python argparse_args.py --host "localhost"
The host is localhost
```



### 选择

ArgumentParser能够指定一个选项只能是枚举集合中的一个枚举值。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('--cheese',
                        choices = ('american','cheddar','provolone','swiss'),
                        default='swiss',
                        dest='cheese',
                        help='The host to connect to.Defaults to localhost',
                        type=str
                        )
    args = parser.parse_args()
    
    print('You have chosen %s cheese '%args.cheese)
```

```bash
$python argparse_choices.py
You have chosen swiss cheese 
$python argparse_choices.py --cheese provolone
You have chosen provolone cheese 
```

如果尝试提供一个在选项列表中不可用的值，将会报错。

### 接受多个值

argparse的一个额外功能是可以指定一个选项接受多个参数。可以设置一个选项接受非绑定数量的参数，或确切几个参数。可以是用哪个add_argument方法的nargs关键字参数实现。

nargs最简单的用法是指定一个选项接受特定数量的参数。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('--madlib',
                        default=['fox','dogs'],
                        dest='madlib',
                        help='Two words to place in the madlib',
                        nargs=2,
                        )
    args = parser.parse_args()
    
    print('The quick brown {0} jumped over the lazy {1}.'.format(*args.madlib))
```

给nargs发送一个整数意味着选项期望参数的具体个数，并将以列表的形式返回这些参数。

```bash
$python argparse_multiargs.py
The quick brown fox jumped over the lazy dogs.

$python argparse_multiargs.py --madlib pirate ninjas
The quick brown pirate jumped over the lazy ninjas.
```

如果参数不是两个，将会报错。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('--addends',
                        dest='addends',
                        help='Integers to provide a sum of',
                        nargs='+',
                        required=True,
                        type=int
                        )
    args = parser.parse_args()
    
    print('%s = %d '%(' + '.join([str(i) for i in args.addends]),sum(args.addends)))
```

```bash
$python argparse_sum.py --addends 1 2 5
1 + 2 + 5 = 8
$python argparse_sum.py --addends 1 2
1 + 2 = 3
$python argparse_sum.py --addends 1 
1 = 1
```



## 位置参数

使用argparse时，必须显式声明位置参数。如果没有显式声明，解析器在完成解析后，并不期望剩下任何参数，如果参数仍存在，则报错。

位置参数的声明等价于选项的声明，只是省略了开头的连字符。例如，前面示例中的--addends选项的形式就比较糟糕。选项应该是可选的。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('addends',
                        help='Integers to provide a sum of',
                        nargs='+',
                        type=int
                        )
    args = parser.parse_args()
    
    print('%s = %d '%(' + '.join([str(i) for i in args.addends]),sum(args.addends)))
```

代码几乎相同，只有--addends参数被替换为addends，没有了双连字符的前缀。这导致解析器期望一个位置参数。

```bash
$python argparse_sum.py 1 2 5
1 + 2 + 5 = 8
```



## 读取文件

编写CLI应用程序时一个常见的需求就是读取文件。argparse模块提供了一个可以被发送给add_argument方法中type关键字参数的特殊类，argparse.FileType。

argparse.FileType类期望参数被发送给python的open函数，不包括文件名称（文件名称由用户在调用程序时提供）。

```python
import argparse

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('-c','--config-file',
                        dest='config',
                        help='The configuration file to use.',
                        default='/etc/cli_script',
                        type=argparse.FileType('r')
                        )
    args = parser.parse_args()
    
    print(args.config.read())
    
```

默认情况下将会从/etc/cli_script读取，但可以使用-c或--config-file选项指定一个不同的文件。

```bash
$echo "This is my config file." > foo.txt
$python cli_script.py --config-file foo.txt
This is my config file.
```






































