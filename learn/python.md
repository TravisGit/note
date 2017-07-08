



# 廖雪峰
**教程是基于python3的**

python官网提供的成功python案例：https://www.python.org/about/success/



python解释器很多，无非就是把python编译到不同目标码执行：
* Cpython：默认的python解释器
* IPython：交互式解释器
* PyPy：采用JIT，有很多坑
* Jython：编译到java字节码执行
* IronPython：运行在.Net平台

## 基础部分
简单语法：
开头为注释
大小写敏感
使用缩进来组织代码块

### 数据类型
* 整数：1 -80 0xff00 无大小限制
* 浮点数：1.23 1.23e9
* 字符串：单引号或双引号括起来的任何文本
* 布尔值：True、False
* 空值：None，None不能理解为0，就是一个特殊的空值
* 无限大：inf

### 变量与常量
* 变量：变量名必须是大小写英文、数字和_的组合，且不能数字开头
* 常量：通常用全大写的变量名表示

### 字符串
* ord()函数获取字符的整数表示，如asc码
* chr()函数把编码转换为对应的字符
* 如果知道字符的整数编码，还可以用十六进制这么写str
```
>>> '\u4e2d\u6587'
'中文'

```
* 当Python解释器读取源代码时，为了让它按UTF-8编码读取，我们通常在文件开头写上这两行
```
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

```
* 格式化 %d-整数 %f-浮点数 %s-字符串 %x-16进制；如果你不太确定应该用什么，%s永远起作用

### list tuple
tuple所谓的“不变”是说，tuple的每个元素，指向永远不变。即指向'a'，就不能改成指向'b'，指向一个list，就不能改成指向其他对象，但指向的这个list本身是可变的！

### dict set
Python内置了字典：dict的支持，dict全称dictionary，在其他语言中也称为map，使用键-值（key-value）存储，具有极快的查找速度。

注意：
* 需要牢记的第一条就是dict的key必须是不可变对象
* 在Python中，字符串、整数等都是不可变的，因此，可以放心地作为key。而list是可变的，就不能作为key

和list比较，dict有以下几个特点：
* 查找和插入的速度极快，不会随着key的增加而变慢；
* 需要占用大量的内存，内存浪费多。

而list相反：
* 查找和插入的时间随着元素的增加而增加；
* 占用空间小，浪费内存很少。

set和dict类似，也是一组key的集合，但不存储value。由于key不能重复，所以，在set中，没有重复的key。

set需要基于list创建：
```
>>> s = set([1, 2, 3])
>>> s
{1, 2, 3}

```
set和dict的唯一区别仅在于没有存储对应的value，都是散列表


### 再议不可变对象

所以，对于不变对象来说，调用对象自身的任意方法，也不会改变该对象自身的内容。相反，这些方法会创建新的对象并返回，这样，就保证了不可变对象本身永远是不可变的。如'abc'.replace('a', 'A') 是返回了一个新字符串

### 控制语句
* 条件判断：if
```
age = 3
if age >= 18:
    print('adult')
elif age >= 6:
    print('teenager')
else:
    print('kid')

```
* 循环语句：for  while


## 函数

/除法计算结果是浮点数，即使是两个整数恰好整除，结果也是浮点数
还有一种除法是//，称为地板除，两个整数的除法仍然是整数
python2 与 python3的主要区别



















## id()
查看一个对象的id，可以理解为其memory address，是独一无二的

## 函数式编程












# 面试

http://python.jobbole.com/85231/

## 函数参数传递
* python中变量都是对某个具体object的引用
* 函数的传递都是值传递，传递的也就是引用的值
* object包括imutable和mutable 两种
* 传入的是不可变对象的引用，则outter space不受影响；传入的是可变对象的引用，则outter space会受到影响

https://stackoverflow.com/questions/986006/how-do-i-pass-a-variable-by-reference

## 元类（metaclass 非常不常用）

## 方法
Python其实有3个方法,即静态方法(staticmethod),类方法(classmethod)和实例方法,如下:


```
def foo(x):
    print "executing foo(%s)"%(x)
 
class A(object):
    def foo(self,x):
        print "executing foo(%s,%s)"%(self,x)
 
    @classmethod
    def class_foo(cls,x):
        print "executing class_foo(%s,%s)"%(cls,x)
 
    @staticmethod
    def static_foo(x):
        print "executing static_foo(%s)"%x
 
a=A()

```

## 实例变量与类变量
类似于函数传参

## Python自省
这个也是python彪悍的特性.

自省就是面向对象的语言所写的程序在运行时,所能知道对象的类型.简单一句就是运行时能够获得对象的类型.比如type(),dir(),getattr(),hasattr(),isinstance().

## 列表推导式 和 字典推导式	
d = {key: value for (key, value) in iterable}

## 迭代器和生成器
* 所有用于for in的，都是迭代器
* 生成器是特殊的迭代器
* 生成器只能便利一遍。

https://stackoverflow.com/questions/231767/what-does-the-yield-keyword-do-in-python
* Iterables： When you create a list, you can read its items one by one. Reading its items one by one is called iteration； Everything you can use "for... in..." on is an iterable; lists, strings, files...

* Generators： Generators are iterators, but you can only iterate over them once. It's because they do not store all the values in memory, they generate the values on the fly

* Yield： yield is a keyword that is used like return, except the function will return a generator.

## *args **kwargs
http://stackoverflow.com/questions/3394835/args-and-kwargs

* *args = list of arguments -不确定多少个参数
* **kwargs = dictionary -字典形式传入参数
* You can also use both in the same function definition but *args must occur before **kwargs

```python
In [1]: def foo(*args):
   ...:     for a in args:
   ...:         print a

In [5]: def bar(**kwargs):
   ...:     for a in kwargs:
   ...:         print a, kwargs[a]
```

解包：Another usage of the *l idiom is to unpack argument lists when calling a function.
```
In [9]: def foo(bar, lee):
   ...:     print bar, lee
   ...:     
   ...:     

In [10]: l = [1,2]

In [11]: foo(*l)
1 2

```

[为什么Python不支持重载](https://www.zhihu.com/question/20053359)

## 面向切面编程AOP和装饰器

概括的讲，装饰器的作用就是为已经存在的对象添加额外的功能。

装饰器是一个很著名的设计模式，经常被用于有切面需求的场景，较为经典的有插入日志、性能测试、事务处理等。装饰器是解决这类问题的绝佳设计，有了装饰器，我们就可以抽离出大量函数中与函数功能本身无关的雷同代码并继续重用。

https://www.zhihu.com/question/24863332

## 鸭子类型
我们并不关心对象是什么类型，到底是不是鸭子，只关心行为。
只要有对应的方法（属性），就可以做对应的事

比如有\__iter__方法就可以用于for in
有\__add__就可以参与+运算
有\__getitem__就可以使用[]

## 新式类和经典类
* python在2.2版本中引入了descriptor功能，也正是基于这个功能实现了新式类(new-styel class)的对象模型
* 新式类是在创建的时候继承内置object对象（或者是从内置类型，如list,dict等），而经典类是直接声明的。

```python
>>> class C:
...     pass
...
>>> dir(C)          //经典类
['__doc__', '__module__']
>>> class B(object):
...     pass
...
>>> dir(B)          //新式类
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribute__', '__hash__', '__init__', '__module__', '__n
ew__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__']
>>>
```

## \__slot__

通常每一个实例x都会有一个__dict__属性，用来记录实例中所有的属性和方法，也是通过这个字典，
可以让实例绑定任意的属性。而__slots__属性作用就是，当类C有比较少的变量，而且拥有__slots__属性时，
类C的实例 就没有__dict__属性，而是把变量的值存在一个固定的地方。如果试图访问一个__slots__中没有
的属性，实例就会报错。这样操作有什么好处呢？__slots__属性虽然令实例失去了绑定任意属性的便利，
但是因为每一个实例没有__dict__属性，却能有效节省每一个实例的内存消耗，有利于生成小而精
干的实例。
 
为什么需要这样的设计呢？
在一个实际的企业级应用中，当一个类生成上百万个实例时，即使一个实例节省几十个字节都可以节省一大笔内存，这种情况就值得使用__slots__属性。

## 单例模式
必须掌握：http://python.jobbole.com/85231/
* 使用__new__方法
* 共享属性
* 装饰器版本
* import方法

## Python里的拷贝
引用和copy(),deepcopy()的区别

```python
import copy
a = [1, 2, 3, 4, ['a', 'b']]  #原始对象

b = a  #赋值，传对象的引用
c = copy.copy(a)  #对象拷贝，浅拷贝
d = copy.deepcopy(a)  #对象拷贝，深拷贝

a.append(5)  #修改对象a
a[4].append('c')  #修改对象a中的['a', 'b']数组对象

print 'a = ', a
print 'b = ', b
print 'c = ', c
print 'd = ', d

输出结果：
a =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
b =  [1, 2, 3, 4, ['a', 'b', 'c'], 5]
c =  [1, 2, 3, 4, ['a', 'b', 'c']]
d =  [1, 2, 3, 4, ['a', 'b']]
```

## 垃圾回收机制
Python GC主要使用引用计数（reference counting）来跟踪和回收垃圾。在引用计数的基础上，通过“标记-清除”（mark and sweep）解决容器对象可能产生的循环引用问题，通过“分代回收”（generation collection）以空间换时间的方法提高垃圾回收效率。

1 引用计数

PyObject是每个对象必有的内容，其中ob_refcnt就是做为引用计数。当一个对象有新的引用时，它的ob_refcnt就会增加，当引用它的对象被删除，它的ob_refcnt就会减少.引用计数为0时，该对象生命就结束了。

优点:

简单
实时性
缺点:

维护引用计数消耗资源
循环引用
2 标记-清除机制

基本思路是先按需分配，等到没有空闲内存的时候从寄存器和程序栈上的引用出发，遍历以对象为节点、以引用为边构成的图，把所有可以访问到的对象打上标记，然后清扫一遍内存空间，把所有没标记的对象释放。

3 分代技术

分代回收的整体思想是：将系统中的所有内存块根据其存活时间划分为不同的集合，每个集合就成为一个“代”，垃圾收集频率随着“代”的存活时间的增大而减小，存活时间通常利用经过几次垃圾回收来度量。

Python默认定义了三代对象集合，索引数越大，对象存活时间越长。

举例：
当某些内存块M经过了3次垃圾收集的清洗之后还存活时，我们就将内存块M划到一个集合A中去，而新分配的内存都划分到集合B中去。当垃圾收集开始工作时，大多数情况都只对集合B进行垃圾回收，而对集合A进行垃圾回收要隔相当长一段时间后才进行，这就使得垃圾收集机制需要处理的内存少了，效率自然就提高了。在这个过程中，集合B中的某些内存块由于存活时间长而会被转移到集合A中，当然，集合A中实际上也存在一些垃圾，这些垃圾的回收会因为这种分代的机制而被延迟。


## List的原理，实现
http://www.jianshu.com/p/J4U6rR


## pip install过慢

http://www.cnblogs.com/microman/p/6107879.html
更换源：Linux下，修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)

内容如下：

[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com