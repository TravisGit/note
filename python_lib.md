

Maching Learning需要掌握的python库：
* pandas
* numpy scipy
* matplotlib



## matplotlib

matplotlib库的pyplot模块提供了与matlab类似的绘图api，方便快速绘制2D图表，[简单demo](http://old.sebug.net/paper/books/scipydoc/matplotlib_intro.html)：

```
# -*- coding: utf-8 -*-
import numpy as np
import matplotlib.pyplot as plt

# 生成数据
x = np.linspace(0, 10, 1000)
y = np.sin(x)
z = np.cos(x**2)

# 图表尺寸
plt.figure(figsize=(8,4))

# 画图
plt.plot(x,y,label="$sin(x)$",color="red",linewidth=2)
plt.plot(x,z,"b--",label="$cos(x^2)$")

# X、Y坐标以及图表名
plt.xlabel("Time(s)")
plt.ylabel("Volt")
plt.title("PyPlot First Example")

# Y轴范围
plt.ylim(-1.2,1.2)
plt.legend()

# 显示
plt.show()
```

matplotlib官方网站提供了很多[example](http://matplotlib.org/examples/index.html)

如果对图的类型不清楚，matplotlib也提供了带图示的demo，非常方便选择：[gallery](http://matplotlib.org/gallery.html)

对于matplotlib库，我们不需要特别的花精力去学，用到的时候去gallery找示例代码就好了。


## numpy

官方文档：[quickstart](https://docs.scipy.org/doc/numpy-dev/user/quickstart.html)

numpy是数值计算库，主要是矩阵运算。核心数据结构
* numpy.ndarray
* numpy.matrixlib.defmatrix.matrix

注意，对数组的操作（除非进行deep copy），数据是共享的
```
b = a.reshape(3,4)          
a[0] = 0
b[0][0]  # is 
```

**基础操作**
functions | description
---------|----------
 基础属性 | ndim shape size
 数组创建 | 从列表创建<br>zeros ones empty<br>np.arrange np.linspace
 数值计算 | a-b a**2 np.sin(a)<br>a*b a\*=3 元素相乘<br>a.dot(b) np.dot(a,b) 矩阵乘法<br>a.sum() a.max() a.min()<br>np.exp(a) np.sqrt(a)
 索引切片 | 与列表一致
 多维数组 | np.fromfunction 从函数创建
 形状变换 | reshape resize<br>a.ravel() 一维展开<br>
 数组合并 | np.vstack np.hstack
 深浅拷贝 | a.view a.copy
 格式转换 | np.mat a.astype
 数据查询 | all any nonzero where
 排序操作 | argsort sort

 所有方法列表可参考：[Routines](https://docs.scipy.org/doc/numpy-dev/reference/routines.html#routines)

**帮助文档查看**：
* help: 查看帮助文档，help(np.linspace)
* lookfor: 根据关键字查找模块，np.lookfor("linear")，会返回所有帮助文档中包含了"linear"，关键字的模块
* info：查看对象详细信息。np.info(a)，会返回对象所有信息；info(np.linspace)与help功能一致
* source：查看源码。但只能查看用python实现的源码，如np.source(np.interp)

**高级特性——[Broadcasting rules](https://docs.scipy.org/doc/numpy-dev/user/basics.broadcasting.html)**

广播特性：不同维度和形状的数组仍可用于计算。See also：numpy.broadcast

好处在于：你不总是需要重塑或铺平数组，使其他形状匹配以用作计算。这样就避免的过多的数据拷贝，广播并不会做无用的拷贝，提升了性能。See also：[如何获得numpy的最佳性能](http://python.jobbole.com/81310/)

官方文档对于广播的描述非常清晰：
When operating on two arrays, NumPy compares their shapes element-wise. It _**starts with the trailing dimensions**_, and works its way forward. Two dimensions are compatible when

* they are equal, or
* one of them is 1

Here is a example：

```
A      (4d array):  8 x 1 x 6 x 1
B      (3d array):      7 x 1 x 5
Result (4d array):  8 x 7 x 6 x 5

A      (4d array):  1 x 8 x 6 x 1
B      (3d array):      7 x 1 x 5
Result (4d array):  ValueError

```

_补充内容（可以跳过）_

np.broadcast可以显示出广播是如何作用的，np.info(np.broadcast)：
```
>>> x = np.array([[1], [2], [3]])
>>> y = np.array([4, 5, 6])
>>> b = np.broadcast(x, y)

>>> out = np.empty(b.shape)
>>> out.flat = [u+v for (u,v) in b]
>>> out
array([[ 5.,  6.,  7.],
       [ 6.,  7.,  8.],
       [ 7.,  8.,  9.]])
```

b中包含广播作用在x，y上生效的结果，list(b)：
```
>>> list(b)
[(1, 4), (1, 5), (1, 6), (2, 4), (2, 5), (2, 6), (3, 4), (3, 5), (3, 6)]

```

**高级特性——[Fancy indexing](https://docs.scipy.org/doc/numpy-dev/user/quickstart.html)**
* indexed by arrays of integers
* indexed by arrays of booleans.
* Indexing with Arrays of Indices

第三种相对少见，属于高级用法，简单用法（更复杂的可参考标题链接）：
```
>>> palette = np.array( [ [0,0,0],                # black
...                       [255,0,0],              # red
...                       [0,255,0],              # green
...                       [0,0,255],              # blue
...                       [255,255,255] ] )       # white
>>> image = np.array( [ [ 0, 1, 2, 0 ],           # each value corresponds to a color in the palette
...                     [ 0, 3, 4, 0 ]  ] )
>>> palette[image]                            # the (2,4,3) color image
array([[[  0,   0,   0],
        [255,   0,   0],
        [  0, 255,   0],
        [  0,   0,   0]],
       [[  0,   0,   0],
        [  0,   0, 255],
        [255, 255, 255],
        [  0,   0,   0]]])
>>> palette[image]                            # the (2,4,3) color image
array([[[  0,   0,   0],
        [255,   0,   0],
        [  0, 255,   0],
        [  0,   0,   0]],
       [[  0,   0,   0],
        [  0,   0, 255],
        [255, 255, 255],
        [  0,   0,   0]]])        

```

A[B]中，B可以是1维，可以是多维(A[B,C])。

```
A      (4d array):  8 x 5 x 6
B      (3d array):  2 x 3
C      (3d array):  2 x 3
A[B]   (4d array):  2 x 2 x 5 x 6
A[B,C] (4d array):  2 x 3 x 6

如下相等关系：
A[B][i][j] = A[B[i][j]]
A[B,C][i][j] = A[B[i][j],C[i][j]]


```

data.argmax(axis=0): 取每列最大值下标

**高级特性——[The ix_() function](https://docs.scipy.org/doc/numpy-dev/user/quickstart.html)**


当需要计算$a + b * c$时，结果满足：
$result[3,2,4] = a[3]*b[2]*c[4]$

np.ix_输入必须是一维，输出打散成N维；输入$N * 1$，输出$N * 1$

```
>>> a = np.array([2,3,4,5])
>>> b = np.array([8,5,4])
>>> c = np.array([5,4,6,8,3])
>>> ax,bx,cx = np.ix_(a,b,c)
>>> ax
array([[[2]],
       [[3]],
       [[4]],
       [[5]]])
>>> bx
array([[[8],
        [5],
        [4]]])
>>> cx
array([[[5, 4, 6, 8, 3]]])
>>> ax.shape, bx.shape, cx.shape
((4, 1, 1), (1, 3, 1), (1, 1, 5))
>>> result = ax+bx*cx
>>> result
array([[[42, 34, 50, 66, 26],
        [27, 22, 32, 42, 17],
        [22, 18, 26, 34, 14]],
       [[43, 35, 51, 67, 27],
        [28, 23, 33, 43, 18],
        [23, 19, 27, 35, 15]],
       [[44, 36, 52, 68, 28],
        [29, 24, 34, 44, 19],
        [24, 20, 28, 36, 16]],
       [[45, 37, 53, 69, 29],
        [30, 25, 35, 45, 20],
        [25, 21, 29, 37, 17]]])
>>> result[3,2,4]
17
>>> a[3]+b[2]*c[4]
17

```

**拼接——Vector Stacking**

```
x = np.arange(0,10,2)                     # x=([0,2,4,6,8])
y = np.arange(5)                          # y=([0,1,2,3,4])
m = np.vstack([x,y])                      # m=([[0,2,4,6,8],
                                          #     [0,1,2,3,4]])
xy = np.hstack([x,y])                     # xy =([0,2,4,6,8,0,1,2,3,4])

```




**最后，如果你有兴趣，可以向numpy贡献源码**：[Contributing to NumPy](https://docs.scipy.org/doc/numpy-dev/dev/)