---
title: Python的tips（持续更新）
categories: python
tags:
- 编程相关
---

# 1. 代码中检查Python的版本

我们可以使用以下代码来检查python版本，来编写各个版本兼容的代码：

```python
if not sys.version_info > (2, 7):
  # berate your user for running a 10 year 
  # python version
elif not sys.version_info >= (3, 5):
  # Kindly tell your user (s)he needs to upgrade 
  # because you're using 3.5 features
```

# 2. 使用IPython

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

# 3. 列表推导式

列表推导式可以代替不简洁的for 循环，列表推导式的基本规则如下：

```python
[expression for item in list if conditional]
```

例如：

```python
filtered = [i for i in range(20) if i % 2 == 0]
print(filtered)
```

# 4. 检查对象的内存占用

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

# 5. 返回多个值

Python 的函数可以不通过list，dict，或者class来返回多个值，如下：

```python
def get_user(id):
  # fetch user from database
  # .....
  return name, brithdate
name, birthdate = get_user(4)
```

对于返回的值，有限制数量是最好的，超过三个返回值应该被放在一个类中。

# 6. 使用data class

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

# 7. Inplace 变量交换

```python
a = 1
b = 2
a, b = b, a
print(a)
# 2
print(b)
# 1
```

# 8. 合并字典(Python3.5+)

```python
dict1 = {'a':1, 'b':2}
dict2 = {'b':3, 'c':4}
merged = {**dict1, **dict2}
print(merged)
# {'a': 1, 'b': 3, 'c': 4}
```

如果存在相同的key，来自第一个字典的key会被覆盖。

# 9. String to title case

```python
mystring = "10 awesome python tricks"
print(mystring.title())
#'10 Awesome Python Tricks'
```

# 10. 将字符串变成list

```python
mystring = "The quick brown fox"
mylist = mystring.split(' ')
print(mylist)
# ['The', 'quick', 'brown', 'fox']
```

# 11. 将字符串列表合并成字符串

和上个trick相反，我们将列表里的字符串合并，并用空格来分离，代码如下：

```python
mylist = ['The', 'quick', 'brown', 'fox']
mystring = " ".join(mylist)
print(mystring)
# 'The quick brown fox'
```

你可能会想，为什么不用`mylist.join(' ')`-- 好问题！

是因为`String.join()`不仅仅可以作用在`list`上，还可以作用在任何可以迭代的变量上。这样写增加了程序的可扩展性。

# 12. Emoji

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

# 13. 将list 切片

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

# 14. 将字符串和list逆序

你可以用slice 来对字符串或者list逆序，通过使用negative step `-1` 比如：

```python
revstring = "abcdefg"[::-1]
print(revstring)

revarray = [1,2,3,4,5][::-1]
print(revarray)
```

# 15. 显示小猫咪

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

# 16. 使用map()

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

# 17. 从一个list 和字符串得到不重复元素

通过`set()` 函数，你可以从list 或者 list-like 对象得到不重复的元素，示例如下：

```python
mylist = [1, 1, 2, 3, 4, 5, 5, 5, 6, 6]
print (set(mylist))
# {1, 2, 3, 4, 5, 6}

# And since a string can be treated like a # list of letters, you can also get the # unique letters from a string this way: 
print (set("aaabbbcccdddeeefff")) 
# {'a', 'b', 'c', 'd', 'e', 'f'}
```

# 18. 寻找最频繁出现的元素的次数

为了寻找list或者string中出现最频繁的值(value), 我们可以编写代码如下：

```python
test = [1, 2, 3, 4, 2, 2, 3, 1, 4, 4, 4]
print(max(set(test), key = test.count))
```

- `max()`会返回list中最大的值，其中`key` 参数接受一个单参数函数来定制排序顺序。在这个例子中为`test.count`. 这个函数会施加在可迭代对象中的每个元素上 (类似于map)。

- `test.count` 是list的内置函数，它会返回输入参数值在当前list中出现的次数。举例来说，`test.count(1)` 会返回2，而`test.count(4)` 会返回4。

- `set(test)` 从test中返回不重复的元素：`1,2,3,4`.

# 19. 创建一个进度条

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

# 20.  在交互shell中使用_

我们可以使用下划线得到上一个表达式的结果，在ipython中，如下：

```python
ln [1]:3 * 3
Out[1]: 9
    
ln [2]:_ + 3
Out[2]: 12
```

这个在python shell中同样成立。同时我们也可以指定`out[n]`得到指定表达式的结果。

# 21. 快速搭建一个网络服务器
你可以快速启动一个网络服务器，分享当前工作目录下的文件：
```bash
python3 -m http.server
```

# 22. 条件三元运算符
这里有另一种方法使得你的代码简介易读

```python
[on_true] if [expression] else [on_false]
```
举个例子：
```python
x = "Success!" if (y == 2) else "failed"
```

# 23. 计算list元素的频率
你可以使用`collections` 库里的`Counter` 来返回一个字典，包含每个唯一的元素出现的
频次：
```python
from collections import Counter

mylist = [1, 1, 2, 3, 4, 5, 5, 5, 6, 6]
c = Counter(mylist)
print(c)
# Counter({1: 2, 2: 1, 3: 1, 4: 1, 5: 3, 6: 2})

# And it works on strings too:
print(Counter("aaaaabbbbbccccc"))
# Counter({'a': 5, 'b': 5, 'c': 5})
```

# 24. 使用@property装饰器
我们可以使用@property装饰器，来限制类中元素值
```python
class Student(object):
    @property
    def birth(self):
        return self._birth

    @birth.setter
    def birth(self, value):
        if value >= 100:
          print ("error")
          self._birth = None
        else:
          self._birth = value

    @property
    def age(self):
        return 2014 - self._birth

xiaoming = Student()
## That is ok
xiaoming.birth = 90 
## Error
# xiaoming.birth = 190
## 由于property装饰，不需要加()
print(xiaoming.age)
```

上面的`birth`是可读写属性，而`age`就是一个只读属性，`age`可以根据`birth`和当前时间计算出来。
上述代码实现了：1）使用`@birth.setter`装饰器和`@property`来限制birth的值。2）使用`@property`来限制对age的写功能。

# 25. 使用namedtuple
`namedtuple` 创造了一个类，并预先定义了元素，格式如下:
```python
collections.namedtuple(typename, field_names, verbose=False, rename=False) 
```
示例：
```python
from collections import namedtuple

# 两种方法来给 namedtuple 定义方法名
User = namedtuple('User', ['name', 'age', 'id'])
user = User('tester', '22', '464643123')

print(user)
# User(name='tester', age='22', id='464643123')
```
# 26. 串联比较运算符
我们可以在python中串联比较运算符，使得整个程序简洁易懂：
```python
x = 10
# Instead of:
if x > 5 and x < 15:
  print("Yes")
# yes

if 5 < x < 15:
 print("Yes")
# Yes
```

# 27. 使用joblib 库来实现程序并行

使用之前可以在自己的环境里先安装好这个库：

```bash
pip install joblib
```

其中joblib中最常用的类和方法分别是Parallel和delayed。`Parallel`主要用于初始化并行计算的参数。`delayed`则主要用来指定需要被并行的参数。示例程序如下:

```python
from math import sqrt
from joblib import Parallel, delayed
Parallel(n_jobs=2)(delayed(sqrt)(i ** 2) for i in range(10))
# [0.0, 1.0, 2.0, 3.0, 4.0, 5.0, 6.0, 7.0, 8.0, 9.0]
```

其中`Parallel(n_jobs=2)`为指定两个cpu，`delayed(sqrt)`表示要并行的函数为`sqrt`。`(i ** 2) for i in range(10)`表示输入的参数为`i ** 2`，其中`i in range(10)`。

# 28. 使用Filter 进行元素过滤

我们可以使用`filter`函数过滤掉不符合我们定义条件的元素。其中第一个参数为function ，即我们自定义的判断函数。第二个参数为可迭代的对象。

```python
def is_odd(n):
    return n % 2 == 1
 
newlist = filter(is_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
print(newlist)
# [1, 3, 5, 7, 9]
```

### 参考资料

[1]. 30 Python Best Practices, Tips, And Tricks. Erik-Jan van Baaren