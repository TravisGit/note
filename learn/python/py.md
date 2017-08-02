






## 文档

官方文档讲解的非常清楚，而且提供了很多工具和示例，常用的整理如下（所有文档可以在左上角选择对应的python版本：2.7 3.3 3.4等）：
* 官网：<https://www.python.org>
* 文档汇总：<https://docs.python.org/3/>
* 官方教程：[Python Tutorial](https://docs.python.org/3/tutorial/index.html#tutorial-index)
* 语言特性：[Language Reference](https://docs.python.org/3/reference/index.html)，其中包含有文法产生式
* 标准库文档：[Standard Library](https://docs.python.org/dev/library/index.html)，描述语法、语义、标准库、调试方法、线程并发等等，非常全面，最常用的re、string等库
* 编码规范：[pep8标准](https://www.python.org/dev/peps/pep-0008)，这个网站刷的太慢了
* 开发工具：[Development Tools](https://docs.python.org/dev/library/development.html)，给出开发用到的基础工具介绍

Tools | Desc 
---------|:----------:
 pydoc | takes a module and generates documentation<br> based on the module’s contents
 doctest<br>unitest | contains frameworks for writing unit tests <br>that automatically exercise code and verify that the expected output is produced
 2to3 | translate Python 2.x source code into valid Python 3.x code
* 使用C/C++：[如何在Python中使用C/C++实现模块](https://docs.python.org/3/extending/index.html#extending-index)
* 中文博客：廖雪峰python教程，很好的中文教程
* Python2与3区别：<https://docs.python.org/dev/library/2to3.html>
* 错误异常：[常见Exception以及其继承关系](https://docs.python.org/3/library/exceptions.html#exception-hierarchy)


## ticks
从开源库的源码中，可以看到很多ticks，比如numpy源码等。
* 判断是否为函数：[stackoverflow](https://stackoverflow.com/questions/624926/how-to-detect-whether-a-python-variable-is-a-function/4234284#4234284)
```python
import collections
isinstance(obj, collections.Callable)

```

## 内置模块
\__built_in__模块中包含了所有内置模块，使用dir(\__built_in__)可以查看所有内置模块

```python
>>> b2 = dir(__builtin__)
>>> b2=b2[b2.index('abs'):]
>>> len(b2)
85

>>> import builtins
>>> b3 = dir(builtins)
>>> b3=b3[b3.index('abs'):]
>>> len(b3)
72

>>> i = set(b2) & set(b3)
>>> u = set(b2) | set(b3)
>>> d = set(b2) ^ set(b3)
>>> len(i),len(u),len(d)
(68, 89, 21)

详情可参见[官网](https://docs.python.org/3/library/functions.html)

```

Python2 | Python3 | description
---------|----------|---------
abs | abs | 求绝对值
all | all | 判断是否全为True
any | any | 判断是否有一个为True
apply |  | 废弃
 x | ascii | 
basestring |  | 判断对象是否为str
bin | bin | 返回二进制表示
bool | bool | 判断是否为真
buffer |  | 
bytearray |  | 字节数组可变，字符串不可变
x | bytearray | 返回字节组数<br>bytearray('中文','utf-8')
bytes |  | 相当于str，转换为字符串
x | bytes | 返回编码<br>bytes('中文','utf-8')
callable | callable | 是否是可调用的<br>funtion or instances with \__call__ method
chr |  | ascii编码转为字符， < 256
x | chr | unicode编码转字符，<0x10ffff
classmethod | classmethod | 将方法转换为类方法<br>与@classmethod效果一致
cmp |  | 普通的比较函数，可以比较int，str等
coerce |  | coerce(2,3,2) 得到(2.0, 3,2),<br>反映连个数值参与运算时是如何转换的
compile | compile | 将源码编译为code object对象<br>可直接使用exec eval执行
complex | complex | 创建一个复数，如3+4i
copyright | copyright | 打印copyright信息
credits | credits | 打印感谢信息
delattr | delattr | 删除属性<br>delattr(x, 'y') is equivalent to ``del x.y''
dict | dict | 字典
dir | dir | 列出所有属性
divmod | divmod | divmod(x,y)返回商与余数
enumerate | enumerate | 返回枚举对象
eval | eval | 可与compile一同使用
x | exec | 执行code object，与compile搭配使用
execfile |  | Read and execute a Python script from a file.
exit | exit | 用于退出python shell
file |  | 打开文件，推荐open替代该函数
filter | filter | 可用特定函数过滤list, tuple, or string
float | float | Convert a string or number to a floating point number
format | format | 等价与value.\__format__(format_spec)
frozenset | frozenset 
getattr | getattr | getattr(x, 'y') is equivalent to x.y
globals | globals | 返回包含了当前所有全局变量的dict
hasattr | hasattr | Return whether the object has an attribute with the given name.
hash | hash | Return a hash value for the object.<br>对于确定字符串，python2和3的hash结果一致
help | help | 帮助文档 
hex | hex | 以字符串形式返回16进制表示
id | id | 可以理解为返回对象的memory address
input | x | 对输入进行计算，相当于eval(raw_input(prompt))
x | input | 获取输入，相当于python2的raw_input
int | int | 整数
intern |  | 加快查找速度，用处不大
isinstance | isinstance | 判断对象a是否为类B的实例<br>判断a是否为类(B,C...)之一的实例 
issubclass | issubclass | 判断A的是否为B的子类
iter | iter | Get an iterator from an object
len | len | 获取长度,对应属性\__len__
license | license | 查看license以及历代版本
list | list | 列表
locals | locals | 返回一个包含当前作用域所有变量的dict
long |  | long类型
map | map | 对list tuple等统一做function<br>如列表相加： map(lambda x,y: x+y, [1,2], (3,4))
max | max | 返回最大值，完全可用map替代实现
memoryview | memoryview | 详情见[stackoverflow](https://stackoverflow.com/questions/18655648/what-exactly-is-the-point-of-memoryview-in-python)
min | min | 类似max
next | next | Return the next item from the iterator
object | object | The most base type
oct | oct | 八进制显示
open | open | 打开文件
ord | ord | 显示单字符的序号<br>python2支持ascii，python3支持unicode
pow | pow | 乘方
print | print | 打印，python3不支持不加括号print a<br>所以最好都是用print(a)
property | property | 属性设置，推荐与decorator一起用
range | range | python2直接返回整数列表<br>python3返回range对象，可做iter用
raw_input |  |  读取输入
reduce |  | reduce(lambda x, y: x+y, [1, 2, 3, 4, 5]) <br>calculates ((((1+2)+3)+4)+5)
reload |  | 当有修改时，Reload the module. <br> The module must have been successfully imported before.
repr | repr | 返回对象的字符串表示
reversed | reversed | 返回倒叙，iter
round | round | 返回指定精度的数值
set | set | 集合
setattr | setattr | 类似getattr 
slice | slice | 返回一个切片对象<br>a=slice(2,4) b[a]就相当于b[2,4]
sorted | sorted | 返回一个新的排序好的列表
staticmethod | staticmethod | 静态方法
str | str | 字符串
sum | sum | 求和
super | super | 父类相关
tuple | tuple | 元组
type | type | 返回类型，对应\__class__
unichr |  
unicode |  
vars | vars | 返回\__dict__
xrange |  | 返回xrange对象而不是list，类似python3的range
zip | zip | Returns an iterator of tuples, <br>where the i-th tuple contains the i-th element <br>from each of the argument sequences or iterables.




### 函数

高阶函数：既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

```
def add(x, y, f):
    return f(x) + f(y)

```

map/reduce：都是内置函数，对列表（iterable）的操作，一定要用map/reduce，可以将函数f作用到每一个元素

注：python3中reduce不再是内置函数，但可以在functools.reduce中找到它

```python
map(...)
    map(function, sequence[, sequence, ...]) -> list

reduce(...)
    reduce(function, sequence[, initial]) -> value

```

reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
map(f,[x1,x2], (y1,y2))= [f(x1,y1), f(x2,y2)]

实现一个str2int：

from functools import reduce
```python
def str2int(s):
    def fn(x, y):
        return x * 10 + y
    def char2num(s):
        return ord(s)-ord('0')
    return reduce(fn, map(char2num, s))

```

filter
和map()类似，filter()也接收一个函数和一个序列。和map()不同的是，filter()把传入的函数依次作用于每个元素，然后根据返回值是True还是False决定保留还是丢弃该元素。

filter(...)
    filter(function or None, sequence) -> list, tuple, or string

sorted
```python
sorted(...)
    sorted(iterable, cmp=None, key=None, reverse=False) --> new sorted list

>>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower, reverse=True)
['Zoo', 'Credit', 'bob', 'about']

```

[闭包](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431835236741e42daf5af6514f1a8917b8aaadff31bf000)
返回一个函数，不立即执行，而是等到调用了返回函数再执行。
返回闭包时牢记的一点就是：返回函数不要引用任何循环变量，或者后续会发生变化的变量。

### 匿名函数lambda
* 匿名函数不用担心函数名冲突
* 更加简洁，常与map结合使用
* 匿名函数有个限制，就是只能有一个表达式，不用写return，返回值就是该表达式的结果。
```python
>>> f = lambda x: x * x
>>> f
<function <lambda> at 0x101c6ef28>
>>> f(5)
25

```

### 装饰器
对原函数进行适当修饰，如执行前打印log
```python
import functools

def log(text):                      #text为装饰器本身接收到参数
    def decorator(func):            #返回一个装饰器
        @functools.wraps(func)      #将__name__属性赋值到wrapper函数
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator

@log('execute')
def now():
    print('2015-3-25')

>>> now()
execute now():
2015-3-25

```
* @log('execute')相当于 log=log('execute')，产生一个参数为execute的装饰器
* @log now       相当于  now=log(now)

### 偏函数
————就是固定了部分参数的函数
假设要转换大量的二进制字符串，每次都传入int(x, base=2)非常麻烦，于是，我们想到，可以定义一个int2()的函数，默认把base=2传进去：

```python
def int2(x, base=2):
    return int(x, base)

```
functools.partial就是帮助我们创建一个偏函数的，不需要我们自己定义int2()，可以直接使用下面的代码创建一个新的函数int2：

```python
>>> import functools
>>> int2 = functools.partial(int, base=2)
>>> int2('1000000')
64
>>> int2('1010101')
85
```
### 实例函数 类函数 静态函数
```
class A(object):
    def foo(self,x):
        print "executing foo(%s,%s)"%(self,x)

    @classmethod
    def class_foo(cls,x):
        print "executing class_foo(%s,%s)"%(cls,x)

    @staticmethod
    def static_foo(x):
        print "executing static_foo(%s)"%x    

```
Basically @classmethod makes a method whose first argument is the class it's called from (rather than the class instance), @staticmethod does not have any implicit arguments.

classmethod与staticmethod区别：https://stackoverflow.com/questions/136097/what-is-the-difference-between-staticmethod-and-classmethod-in-python


## 模块
一个.py文件就称为一个模块。
好处：模块化，可维护性

```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

' a test module '

__author__ = 'Michael Liao'

import sys

def test():
    args = sys.argv
    if len(args)==1:
        print('Hello, world!')
    elif len(args)==2:
        print('Hello, %s!' % args[1])
    else:
        print('Too many arguments!')

if __name__=='__main__':
    test()

```
* 第1行和第2行是标准注释，第1行注释可以让这个hello.py文件直接在Unix/Linux/Mac上运行，第2行注释表示.py文件本身使用标准UTF-8编码
* 第4行是一个字符串，表示模块的文档注释，任何模块代码的第一个字符串都被视为模块的文档注释
* \__author__变量把作者写进去，这样当你公开源代码后别人就可以瞻仰你的大名

### 包
* 为解决模块同名冲突，引入包。同一个目录下所有模块构成一个包。
* 请注意，每一个包目录下面都会有一个\__init__.py的文件，这个文件是必须存在的，否则，Python就把这个目录当成普通目录，而不是一个包。\__init__.py可以是空文件，也可以有Python代码，因为\__init__.py本身就是一个模块，而它的模块名就是mycompany
* 模块和包名不要与系统模块重复
* [\__init__.py](https://github.com/numpy/numpy/blob/master/numpy/f2py/__init__.py)会在模块被import时调用，因此\__init__.py中可以放一些test代码，检测模块功能是否正常等，也可以提供更丰富报错

包内模块间的相互引用，还有更多细节：[how-to-fix-attempted-relative-import-in-non-package-even-with-init-py](https://stackoverflow.com/questions/11536764/how-to-fix-attempted-relative-import-in-non-package-even-with-init-py)

比较推荐的方法是将包路径加入到sys.path中，来引用包内其他模块：
```python
if __name__ == '__main__' and __package__ is None:
    from os import sys, path
    sys.path.append(path.dirname(path.dirname(path.abspath(__file__))))

```

### 作用域

python中如何分别public与private：
* 所有下划线开头的默认为private，如_xxx
* 其他的默认为public

### 第三方模块
* 第三方库使用pip 包管理工具安装
* 加载模块时，会从sys.path中搜索，如有特殊需求，可以特定目录加入sys.path中：
```
>>> import sys
>>> sys.path
['', '/Library/Frameworks/Python.framework/Versions/3.4/lib/python34.zip', '/Library/Frameworks/Python.framework/Versions/3.4/lib/python3.4', '/Library/Frameworks/Python.framework/Versions/3.4/lib/python3.4/plat-darwin', '/Library/Frameworks/Python.framework/Versions/3.4/lib/python3.4/lib-dynload', '/Library/Frameworks/Python.framework/Versions/3.4/lib/python3.4/site-packages']

```

## 面向对象

实例与类
```python
>>> bart = Student()
>>> bart
<__main__.Student object at 0x10a67a590>
>>> Student
<class '__main__.Student'>

```

初始化方法，\__init__:
```python
class Student(object):
    name = "Student"                    //类属性
    def __init__(self, name, score):
        self.__name = name              //与self绑定，为实例属性
        self.__score = score

s = Student("ao", 19)                   //注意，没有new关键字

```

* 访问控制：类的属性命名以\__开头，则默认为private
* 注意：所有的成员变量都应该是private的，setter和getter应通过property来实现
* 特殊变量：变量名类似__xxx__的，特殊变量是可以直接访问的
* 双下划线开头的实例变量是不是一定不能从外部访问呢？其实也不是。不能直接访问\__name是因为Python解释器对外把\__name变量改成了\_Student__name，所以，仍然可以通过\_Student__name来访问__name变量
* **总的来说就是，Python本身没有任何机制阻止你干坏事，一切全靠自觉**

### 继承和多态
```python
class Animal(object):
    def run(self):
        print('Animal is running...')

class Dog(Animal):

    def run(self):
        print('Dog is running...')

class Cat(Animal):

    def run(self):
        print('Cat is running...')

class Wtf(Dog, Cat):                    //多重继承

    def run(self):
        print('Something is running...')

```
### 静态语言和动态语言

对于静态语言（例如Java）来说，如果需要传入Animal类型，则传入的对象必须是Animal类型或者它的子类，否则，将无法调用run()方法。

对于Python这样的动态语言来说，则不一定需要传入Animal类型。我们只需要保证传入的对象有一个run()方法就可以了：

```python
class Timer(object):
    def run(self):
        print('Start...')

```
这就是动态语言的“鸭子类型”，它并不要求严格的继承体系，一个对象只要“看起来像鸭子，走起路来像鸭子”，那它就可以被看做是鸭子。

* 因此继承的好处是代码复用
* 而多态就不再依赖于继承关系了

## 面向对象高级
数据封装、继承和多态只是面向对象程序设计中最基础的3个概念。在Python中，面向对象还有很多高级特性，允许我们写出非常强大的功能。

### \__slot__
* 类和实例可在运行时任意绑定属性
```python
>>> def set_score(self, score):
...     self.score = score
...
>>> Student.set_score = set_score

```
* 为限制绑定的属性，可定义特殊关键字\__slot__
```python
class Student(object):
    __slots__ = ('name', 'age') # 用tuple定义允许绑定的属性名称

```
* 子类不受父类slot限制；如果子类也定义了\__slot__，则允许定义的属性是子类的\__slots__加上父类的\__slots__

### property
@property是一个内置装饰器，专门设置setter和getter函数，用于封装对属性的访问

```
class Example(object):
    def __init__(self, x=None, y=None):
        self._x = x
        self._y = y

    @property
    def x(self):
        return self.x or self.defaultX()

    @x.setter
    def x(self, value):
        self._x = value

e = Example()
e.x = 1                     //这里会自动调用对应的setter函数

```

### 一些特殊的属性(变量/方法)

[示例](http://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/0014319098638265527beb24f7840aa97de564ccc7f20f6000)

python中的所有（变量、函数）都是对象，有什么属性（方法），就可以被用作什么操作。

attr | 对应功能 
---------|----------
 \__str__ | print(X) 对应 X.\__str__
 \__repr__ | 调试时直接敲变量X 对应 X.\__repr__
 \__iter__<br>\__next__ | 用于for……in
\__getitem__ | X[i] 对应 X.\__getitem(i)
\__getattr__ | X.i 对应 X.\__getattr__(i)
\__call__ | X() 对应 X.\__call__<br>类的话对应的是\__init__

### 元类
动态语言的优势，在运行时可动态创建类，类的创建除了用class关键字，还可以用type方法：

```python
>>> def hello(self):
...     print("hello")
...
>>> Hello = type("Hello",(object, ) , dict(fn=hello))
>>> h = Hello()
>>> h.fn()
hello

```

除了使用type()动态创建类以外，要控制类的创建行为，还可以使用metaclass。元类就是用来创建类的类
定义ListMetaclass，按照默认习惯，metaclass的类名总是以Metaclass结尾，以便清楚地表示这是一个metaclass：

```python
# metaclass是类的模板，所以必须从`type`类型派生：
class ListMetaclass(type):
    def __new__(cls, name, bases, attrs):
        attrs['add'] = lambda self, value: self.append(value)
        return type.__new__(cls, name, bases, attrs)

# 传入metaclass参数
class MyList(list, metaclass=ListMetaclass):
    pass

```

当我们传入关键字参数metaclass时，魔术就生效了，它指示Python解释器在创建MyList时，要通过ListMetaclass.__new__()来创建，在此，我们可以修改类的定义，比如，加上新的方法，然后，返回修改后的定义。

__new__()方法接收到的参数依次是：

* 当前准备创建的类的对象；

* 类的名字；

* 类继承的父类集合；

* 类的方法（属性）集合。

**注：如果不强求改变类创建时的行为，不用强求使用metaclass。为什么ORM需要metaclass，仅仅继承不行吗？
u = User(name='jxk', email='xxx@xx.com', passwd='123')
u.save()
希望可以转换成
insert into User (`name`, `email`, `passwd`) values ('jxk', 'xxx@xx.com', '123')
如果使用继承要解决两个问题：
* 如何获取到name，email这种参数字段的。以及User这种类名，通过传入参数**kw可以解决
* 如何获取类中的所有属性（方法、实例），元类的attrs参数中则有该信息，使用dir(self)和getattr内置函数也可以获取到。

似乎不需要元类也能实现ORM，除非有什么操作必须是类创建时(__new__)要做的，暂时想不到。

### __new__ __init__
__new__是在类创建时调用
__init__是在类初始化时调用
c = C()方式创建实例时，会依次调用__new__ __init__，那么什么时候会只调用__new__，而不调用__init__?
* 仅仅显示调用C.__new__，而不是通过构造函数创建实例时；
* 反序列化，如pickle一个类时
* 重写了__new__方法，且__new__方法没有返回任何实例时，不会调用__init__

如果重写了__new__ __init__，则子类的__init__需要显示调用父类__init__

class ModularTuple(tuple):
    def __new__(cls, tup, size=100):
        tup = (int(x) % size for x in tup)
        return super(ModularTuple, cls).__new__(cls, tup)



## 调试
### 错误以及异常处理
```python
try:
    print('try...')
    r = 10 / int('2')
    print('result:', r)
except ValueError as e:
    print('ValueError:', e)
except ZeroDivisionError as e:
    print('ZeroDivisionError:', e)
else:
    print('no error!')
finally:
    print('finally...')
print('END')

```
### 调试
* print
* assert：如assert n != 0, 'n is zero!'
* logging：输出到文件
* pdb：使用调试器：python3 -m pdb err.py
* pdb.set_trace()：设置断点，运行代码，程序会自动在pdb.set_trace()暂停并进入pdb调试环境，可以用命令p查看变量，或者用命令c继续运行：
```
# err.py
import pdb

s = '0'
n = int(s)
pdb.set_trace() # 运行到这里会自动暂停
print(10 / n)

```
* IDE：推荐！

### 测试

单元测试：[unittest](https://docs.python.org/dev/library/unittest.html)，针对每一个模块，每一个函数写单元测试，测试功能。

* numpy的单元测试示例：https://github.com/numpy/numpy/blob/master/numpy/testing/tests/test_utils.py
* 执行：python -m unittest test_utils
* testcase：可根据输入的不同数据类型、范围，判断是否与结果相符等等

文档测试：[doctest](https://docs.python.org/dev/library/doctest.html)

## IO编程

### 文件读写
* 用上下文管理器，更为简洁：
```
with open('/path/to/file', 'r') as f:
    print(f.read())

```
* read()一次读取文件内容，如果文件10G，内存就爆了；不确定文件大小时，应使用readlines()逐行读取
* 默认读取的都是utf-8编码文件，如读取二进制：
```
>>> f = open('/Users/michael/test.jpg', 'rb')
>>> f.read()
b'\xff\xd8\xff\xe1\x00\x18Exif\x00\x00...' # 十六进制表示的字节

```
* 要读取非UTF-8编码的文本文件，需要给open()函数传入encoding参数，例如，读取GBK编码的文件：
```
>>> f = open('/Users/michael/gbk.txt', 'r', encoding='gbk', errors='ignore')

```

### StringIO
在内存中进行读写，只能操作str
```python
>>> from io import StringIO
>>> f = StringIO('Hello!\nHi!\nGoodbye!')
>>> while True:
...     s = f.readline()
...     if s == '':
...         break
...     print(s.strip())
...
Hello!
Hi!
Goodbye!

```

### BytesIO
在内存中读写，操作二进制数据
```python
>>> from io import BytesIO
>>> f = BytesIO()
>>> f.write('中文'.encode('utf-8'))
6
>>> print(f.getvalue())
b'\xe4\xb8\xad\xe6\x96\x87' 

```

### 序列化
运行时变量都在内存中，程序结束内存就被回收了。为持久化保存，将变量变为可存储或可传输的过程叫序列化
* pickle模块实现序列化
* json模块实现序列化

## 并发：进程线程
* 在Unix/Linux下，可以使用fork()调用实现多进程。
* 要实现跨平台的多进程，可以使用multiprocessing模块。
* 进程间通信是通过Queue、Pipes等实现的。
* 多线程模块：_thread threading，多线程要加锁
* Python解释器由于设计时有[GIL全局锁](http://python.jobbole.com/81822/)，导致了多线程无法利用多核。多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核；在Linux上实际测试并不是如此，每个CPU核都有占用，而且并不固定是某一个CPU核占用高，应该和系统本身也有关系，这个问题待探讨。Linux上实测单线程比多线程更快
* 换言之，由于GIL，Cpython解释器的多线程是假的（在IO密集型才有用）；计算密集型用多进程较好
* ThreadLocal：每个线程私有的变量；可理解为，以Thread本身为key，从全局dict中获取对象
* Python本就不应该做计算密集型任务，而适合做IO密集型

## 常用库
### re模块
* match：判断字符串是否符合某种格式  `re.match(r'^\d{3}\-\d{3,8}$', '010-12345')`
* split：分割 `re.split(r'[\s\,]+', 'a,b, c  d')`
* group：分组，提取子串，子串用()分组  `re.match(r'^(\d{3})-(\d{3,8})$', '010-12345').group()`
* 默认是贪婪匹配，匹配尽可能长的串。加？采用非贪婪匹配
* compile：如果一个表达式使用多次，compile之后再用可提高性能  `re.compile(r'^(\d{3})-(\d{3,8})$')`

### collections
提供了一些有用的集合类

### base64
Base64是一种用64个字符来表示任意二进制数据的方法。

### struct
专门处理字节的数据类型

### itertools
迭代器相关，如把两个迭代器串联成一个更大的迭代器

### contextlib
with关键字的使用：实际上，任何对象，只要正确实现了上下文管理，就可以用于with语句
实现上下文管理是通过\__enter__和\__exit__这两个方法实现的。

但编写\__enter__和\__exit__仍然繁琐，因此Python的标准库contextlib提供了更简单的写法
```python

from contextlib import contextmanager

class Query(object):

    def __init__(self, name):
        self.name = name

    def query(self):
        print('Query info about %s...' % self.name)

@contextmanager
def create_query(name):
    print('Begin')
    q = Query(name)
    yield q
    print('End')

```
@contextmanager这个decorator接受一个generator，用yield语句把with ... as var把变量输出出去，然后，with语句就可以正常地工作了：

很多时候，我们希望在某段代码执行前后自动执行特定代码，也可以用@contextmanager实现（如果是完整函数当然可以用装饰器）。例如：

```python
@contextmanager
def tag(name):
    print("<%s>" % name)
    yield
    print("</%s>" % name)

with tag("h1"):
    print("hello")
    print("world")
```

### urllib
urllib提供了一系列用于操作URL的功能。
urllib的request模块可以非常方便地抓取URL内容，也就是发送一个GET请求到指定的页面，然后返回HTTP的响应：
```python
from urllib import request

with request.urlopen('https://api.douban.com/v2/book/2129650') as f:
    data = f.read()
    print('Status:', f.status, f.reason)
    for k, v in f.getheaders():
        print('%s: %s' % (k, v))
    print('Data:', data.decode('utf-8'))

```

### virtualenv
virtualenv为每一个项目创建一个干净的、不受系统python环境干扰的，python运行环境。

```
$ pip3 install virtualenv               //安装
$ cd projectX
$ virtualenv --no-site-packages venv    //为projectX创建一个本地python环境venv
$ source venv/bin/activate              //进入该环境
$ pip install jinja2                    //进行配置，任意操作
$ deactivate                            //退出该环境

```

### 协程Coroutine
IEnumerator & Coroutine
协程其实就是一个IEnumerator（迭代器），IEnumerator 接口有两个方法 Current 和 MoveNext() ，前面介绍的 TaskManager 就是利用者两个方法对协程进行了管理，只有当MoveNext()返回 true时才可以访问 Current，否则会报错。迭代器方法运行到 yield return 语句时，会返回一个expression表达式并保留当前在代码中的位置。 当下次调用迭代器函数时执行从该位置重新启动。

整个流程无锁，由一个线程执行，produce和consumer协作完成任务，所以称为“协程”，而非线程的抢占式多任务。

### asyncio
asyncio是Python 3.4版本引入的标准库，直接内置了对异步IO的支持。
```python
import threading
import asyncio

@asyncio.coroutine
def hello():
    print('Hello world! (%s)' % threading.currentThread())
    yield from asyncio.sleep(1)     //这里去执行其他协程
    print('Hello again! (%s)' % threading.currentThread())

loop = asyncio.get_event_loop()
tasks = [hello(), hello()]
loop.run_until_complete(asyncio.wait(tasks))
loop.close()

```
如果把asyncio.sleep()换成真正的IO操作（但通常是asyncio库再次封装过的IO操作），则多个coroutine就可以由一个线程并发执行。


为了简化并更好地标识异步IO，从Python 3.5开始引入了新的语法async和await，可以让coroutine的代码更简洁易读。

asyncio实现了TCP、UDP、SSL等协议，aiohttp则是基于asyncio实现的HTTP框架。

## Functional Programming

好处：无变量，无中间状态，可重入（多线程安全）

对应到编程语言，就是越低级的语言，越贴近计算机，抽象程度低，执行效率高，比如C语言；越高级的语言，越贴近计算，抽象程度高，执行效率低，比如Lisp语言。

函数式编程就是一种抽象程度很高的编程范式，纯粹的函数式编程语言编写的函数没有变量，因此，任意一个函数，只要输入是确定的，输出就是确定的，这种纯函数我们称之为没有副作用。而允许使用变量的程序设计语言，由于函数内部的变量状态不确定，同样的输入，可能得到不同的输出，因此，这种函数是有副作用的。

函数式编程的一个特点就是，允许把函数本身作为参数传入另一个函数，还允许返回一个函数！

Python对函数式编程提供部分支持。由于Python允许使用变量，因此，Python不是纯函数式编程语言。


## 函数重载
不支持函数重载，因为可变参数列表已经可以解决这个需求了
>>> def f1(x):
...     return x
... 
>>> def f1(x,y):
...     return x+y
... 
>>> f1(11)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: f1() missing 1 required positional argument: 'y'




## 

文档：
aiomysql：https://aiomysql.readthedocs.io/en/latest/pool.html


sudo pip3.5 install aiohttp jinja2 aiomysql

对应github，以及不同的分支
https://github.com/michaelliao/awesome-python3-webapp/tree/day-09

### mysql
sudo apt-get install mysql-server

mysql -u user -p

执行初始化脚本
mysql -u root -p < schema.sql

登录后，创建xxx db，注意命令都要有;结束
create database xxx;

查看当前有哪些db
show databases;

删除
drop database xxx;


### bug
* 修改www/orm.py
```
             raise
+        finally:
+            conn.close()
         return affected
```