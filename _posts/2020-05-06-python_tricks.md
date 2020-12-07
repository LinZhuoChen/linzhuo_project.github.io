---
title: Python的30个技巧
categories: python
tags:
- 编程相关
---

# 1. 使用Python3

Python2 从 2020年1月1日起就不再被支持，所以本文档的技巧全部基于python3。

# 2. 代码中检查Python的版本

我们可以使用以下代码来检查python版本，来编写各个版本兼容的代码：

```python
if not sys.version_info > (2, 7):
  # berate your user for running a 10 year 
  # python version
elif not sys.version_info >= (3, 5):
  # Kindly tell your user (s)he needs to upgrade 
  # because you're using 3.5 features
```

# 3. 使用IPython

IPython是一个增强后的shell，仅仅因为单独的补全功能就很香了。但是它还有更多的功能，我喜欢它更多是因为其内置的魔法命令，举几个例子：

- `%cd` - 改变当前的工作目录

- `%edit` - 打开编辑器，在关闭编辑器后执行你刚刚输入的代码

- `%env`- 展现当前的环境变量

- `%pip install [pkgs]`- 通过pip安装一些库(不需要离开交互界面)

- `%time` and `%timeit`  - 测试代码的时间

另一个有用的特性是，`In`和 `Out` 都是可以引用的，例如我们可以通过使用`out[3]` 来使用第三行的指令输出。

通过下列命令来ls

安装`IPython`

```bash
pip3 install ipython
```

# 4. 列表推导式

列表推导式可以代替不简洁的for 循环，列表推导式的基本规则如下：

```python
[expression for item in list if conditional]
```

例如：

```python
filtered = [i for i in range(20) if i % 2 == 0]
print(filtered)
```

# 5. 检查对象的内存占用

```python
import sys

mylist = range(0, 10000)
print(sys.getsizeof(mylist))
# 48
```

等等，为什么这么大的list 只有48个字节？

这是因为`range` 返回一个class，它只是表现的像个`list`。`range` 比实际的`list` 更节省内存。

我们也可以用列表推导式来构建一个相同范围的`list`:

```python
import sys
myreallist = [x for x in range(0, 10000)]
print(sys.getsizeof(myreallist))
# 87632
```

# 6. 返回多个值

Python 的函数可以不通过list，dict，或者class来返回多个值，如下：

```python
def get_user(id):
  # fetch user from database
  # .....
  return name, brithdate
name, birthdate = get_user(4)
```

对于返回的值，有限制数量是最好的，超过三个返回值应该被放在一个类中。

# 7. 使用data class

从Python3.7 后，Python 支持data类，使用data class 有以下几个好处：

- data 类只需要实现少量代码。

- 你可以对data class 进行比较，因为`__eq__` 已经为你实现好了。

- 你可以简单的`print` 一个data class 因为`__repr__`已经实现好了。

- 数据类需要类型提示，减少了出现错误的机会。

```python
from dataclasses import dataclass

@dataclass
class Card:
  rank: str
  suit: str
card = Card("Q", "hearts")
print(card == card)
# True

print(card.rank)
# 'Q'
print(card)
Card(rank = 'Q', suit="hearts")
```

一个更深入的介绍可以在[这里](https://realpython.com/python-data-classes/)找到。

# 8. Inplace 变量交换

```python
a = 1
b = 2
a, b = b, a
print(a)
# 2
print(b)
# 1
```

# 9. 合并字典(Python3.5+)

```python
dict1 = {'a':1, 'b':2}
dict2 = {'b':3, 'c':4}
merged = {**dict1, **dict2}
print(merged)
# {'a': 1, 'b': 3, 'c': 4}
```

如果存在相同的key，来自第一个字典的key会被覆盖。

# 10. String to title case

```python
mystring = "10 awesome python tricks"
print(mystring.title())
#'10 Awesome Python Tricks'
```

# 11. 将字符串变成list

```python
mystring = "The quick brown fox"
mylist = mystring.split(' ')
print(mylist)
# ['The', 'quick', 'brown', 'fox']
```

# 12. 将字符串列表合并成字符串

和上个trick相反，我们将列表里的字符串合并，并用空格来分离，代码如下：

```python
mylist = ['The', 'quick', 'brown', 'fox']
mystring = " ".join(mylist)
print(mystring)
# 'The quick brown fox'
```

你可能会想，为什么不用`mylist.join(' ')`-- 好问题！

是因为`String.join()`不仅仅可以作用在`list`上，还可以作用在任何可以迭代的变量上。这样写增加了程序的可扩展性。

# 13. Emoji

首先，我们需要安装`emoji` 库

```bash
pip3 install emoji
```

安装好之后，你可以运行下列代码：

```python
import emoji
result = emoji.emojize('Python is :thumbs_up:')
print(result)

# You can also reverse this:
result = emoji.demojize('python is : （表情）')
```

访问[这里](https://pypi.org/project/emoji/) 获得更多例子和文档

# 14. 将list 切片

基本的list切片语法如下：

```bash
a[start:stop:step]
```

Start, stop 和 step 是可选项，如果你没有添加，他们默认为：

- start = 0
- list 的最后一个为stop
- step=1

代码实例如下：

```python
# We can easily create a new list from
# the first two elements of a list:
first_two = [1, 2, 3, 4, 5][0:2]
print(first_two)
# [1, 2]
# And if we use a step value of 2,
# we can skip over every second number
# like this:
steps = [1, 2, 3, 4, 5][0:5:2]
print(steps)
# [1, 3, 5]

# This works on strings too. In Python,
# you can treat a string like a list of
# letters:
mystring = "abcdefdn nimt"[::2]
print(mystring)
# 'aced it'
```

# 15. 将字符串和list逆序

你可以用slice 来对字符串或者list逆序，通过使用negative step `-1` 比如：

```python
revstring = "abcdefg"[::-1]
print(revstring)

revarray = [1,2,3,4,5][::-1]
print(revarray)
```

# 16. 显示小猫咪

首先我们需要安装 Python Image Library

```bash
pip3 install Pillow
```

我们接下来使用下列代码来展示图片：

```python
from PIL import Image

im = Image.open("kittens.jpg")
im.show()
print(im.format, im.size, im.mode)
# JPEG (1920, 1357) RGB
```

Pillow 不仅仅可以展示图片，它可以分析，重新设定大小，过滤，增强等等。

# 17. 使用map()

Python 中的一个内置函数为`map()`，`map()`的语法如下：

```python
map(function, something_iterable)
```

所以你可以给一个函数去执行，和一些被执行的对象，这些可以是任何可迭代的。在下面的例子里我们使用列表：

```python
def upper(s):
  return s.upper()

mylist = list(map(upper, ['sentence', 'fragment']))
print(mylist)
# ['SENTENCE', 'FRAGMENT']

# Convert a string representation of a number into a list of ints.
list_of_ints = list(map(int, "1234567"))
print(list_of_ints)
# [1, 2, 3, 4, 5, 6, 7]
```

# 18. 从一个list 和字符串得到不重复元素

通过`set()` 函数，你可以从list 或者 list-like 对象得到不重复的元素，示例如下：

```python
mylist = [1, 1, 2, 3, 4, 5, 5, 5, 6, 6]
print (set(mylist))
# {1, 2, 3, 4, 5, 6}

# And since a string can be treated like a # list of letters, you can also get the # unique letters from a string this way: 
print (set("aaabbbcccdddeeefff")) 
# {'a', 'b', 'c', 'd', 'e', 'f'}
```

# 19. 寻找最频繁出现的元素的次数

为了寻找list或者string中出现最频繁的值(value), 我们可以编写代码如下：

```python
test = [1, 2, 3, 4, 2, 2, 3, 1, 4, 4, 4]
print(max(set(test), key = test.count))
```

- `max()`会返回list中最大的值，其中`key` 参数接受一个单参数函数来定制排序顺序。在这个例子中为`test.count`. 这个函数会施加在可迭代对象中的每个元素上 (类似于map)。

- `test.count` 是list的内置函数，它会返回输入参数值在当前list中出现的次数。举例来说，`test.count(1)` 会返回2，而`test.count(4)` 会返回4。

- `set(test)` 从test中返回不重复的元素：`1,2,3,4`.

# 20. 创建一个进度条

我们可以使用`progress` package 来快速实现进度条：

```bash
pip3 install progress
```

我们可以使用下列代码来实现进度条：

```python
from progress.bar import Bar

bar = Bar('Processing', max=20)
for i in range(20):
    # Do some work
    bar.next()
bar.finish()
```

# 21.  在交互shell中使用_

我们可以使用下划线得到上一个表达式的结果，在ipython中，如下：

```python
ln [1]:3 * 3
Out[1]: 9
    
ln [2]:_ + 3
Out[2]: 12
```

这个在python shell中同样成立。同时我们也可以指定`out[n]`得到指定表达式的结果。

# 22. 