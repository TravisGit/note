covariance matrix: 协方差


## 特征值与奇异值：http://blog.jobbole.com/88208/（讲的超好）
* 特征值、特征向量、特征值分解：只能作用与方阵

* 奇异值、奇异值分解（也叫SVD）：适用于任何矩阵
![奇异值分解](http://ww2.sinaimg.cn/mw690/6941baebjw1eu0zutgfq4j20c70483ys.jpg)

* 正交矩阵：构成矩阵的所有向量是正交的，正交矩阵也是方阵，满足：$A * A^T = I$

其实SVD还是可以用并行的方式去实现的，在解大规模的矩阵的时候，一般使用迭代的方法，当矩阵的规模很大（比如说上亿）的时候，迭代的次数也可能会上亿次，如果使用Map-Reduce框架去解，则每次Map-Reduce完成的时候，都会涉及到写文件、读文件的操作。个人猜测Google云计算体系中除了Map-Reduce以外应该还有类似于MPI的计算模型，也就是节点之间是保持通信，数据是常驻在内存中的，这种计算模型比Map-Reduce在解决迭代次数非常多的时候，要快了很多倍。


### 奇异值与主成分分析（PCA）：

PCA的问题其实是一个基的变换，使得变换后的数据有着最大的方差。方差的大小描述的是一个变量的信息量，我们在讲一个东西的稳定性的时候，往往说要减小方差，如果一个模型的方差很大，那就说明模型不稳定了。但是对于我们用于机器学习的数据（主要是训练数据），方差大才有意义，不然输入的数据都是同一个点，那方差就为0了，这样输入的多个数据就等同于一个数据了。

PCA的全部工作简单点说，就是对原始的空间中顺序地找一组相互正交的坐标轴，第一个轴是使得方差最大的，第二个轴是在与第一个轴正交的平面中使得方差最大的，第三个轴是在与第1、2个轴正交的平面中方差最大的，**这样假设在N维空间中，我们可以找到N个这样的坐标轴，我们取前r个去近似这个空间，这样就从一个N维的空间压缩到r维的空间了，但是我们选择的r个坐标轴能够使得空间的压缩使得数据的损失最小**。

PCA的方式有很多，例如：
* 对m*n的源数据矩阵A，求协方差矩阵$B_{n*n}$，再对B进行特征值分解，取最大的前K个特征值对应的特征向量，对源矩阵A做变换。m<n时用这种方式：http://blog.jobbole.com/109015/?utm_source=blog.jobbole.com&utm_medium=relatedPosts

* 对m*n的源数据矩阵A，直接进行奇异值分解，同样去前K个最大奇异值，最总得到PCA结果，相比于第一种方法，cost更高一些。m>n时用这种方式

### word2vec 词向量
word2vec就是用一个一层的神经网络(CBOW的本质)把one-hot形式的词向量映射到分布式形式的词向量，为了加快训练速度，用了Hierarchical softmax，negative sampling 等trick。

关键点在于：输入是什么，输出是什么，loss怎么计算：https://zhuanlan.zhihu.com/p/22477976

### normalization & standardize
标准化和归一化，将所有特征归一化到(0,1)范围内，好处：
* 提升模型精度：避免多特征数值范围相差过大，导致数值较小的特征精度损失很大
* 提升训练速度：未归一化时，模型可以想象为一个长椭圆，梯度下降很慢

常用方法：
* min-max: (x-min)/(max-min)，新加入数据时，需重新计算
* log: logx/logmax
* norm范数：x/二范数，二范数就是向量的模
* atan: atan(X)*2/pi，不用考虑新加入数据重新计算问题
* z-score: (x-mean)/std，经过处理后符合均值为0，标准差为1的正太分布，当有超出取值范围的利群数据时，表现比依赖max的方法好。有些地方也叫standardize
* 激活函数：如sigmoid也可以完成(0,1)区间的映射，但激活函数本身的意义不是用来标准化的，这点要注意。因为标准化是在wx操作之前，而激活是在wx之后做的，softmax(wx+b)



## 方差
### 样本与总体
* 总体方差：计算总体空间中的方差，sum(x-mean)/n
* 样本方差：通过样本空间，对总体方差的无偏估计：sum(x-mean)/(n-1)
* 这里除n和n-1的区别？http://blog.csdn.net/maoersong/article/details/21819957
* 为何分母是n-1就是总体方差的无偏估计，[实际就是证明](https://www.zhihu.com/question/20099757)：$\mathbb{E}\Big[\frac{1}{n-1} \sum_{i=1}^n\Big(X_i -\bar{X}\Big)^2 \Big]=\sigma^2$
* 协方差：也是一样的，有样本与总体之分

### 自由度
average与mean的区别：
* average：基于一定数量的样本通过求平均值得出的summary statistics,是样本统计量(Sample statistics)的样本均值
* mean：后者是作为总体参数（Population parameter）的总体均值

[统计学中自由度的定义](https://www.zhihu.com/question/20983193)，简单理解：n 个样本，如果在某种条件下，样本均值是固定的 (fixed)，那么只剩 n-1 个样本的值是可以变化的。

### numpy中的计算
numpy中的方差，默认是计算总体方差：
```python
>>> a = np.array([1,2,3,4])
>>> a.var()
1.25

```
如果要按照样本方差来计算，可以设定自由度：
```python
>>> a = np.array([1,2,3,4])
>>> a.var(ddof=1)
1.6666666666666667

>>> np.var?
...
ddof : int, optional
    "Delta Degrees of Freedom": the divisor used in the calculation is
    ``N - ddof``, where ``N`` represents the number of elements. By
    default `ddof` is zero.

```

### 实际使用时，该用哪种方差？
通常计算方差用总体，计算协方差时用样本



### 协方差与相关系数
知乎链接：https://www.zhihu.com/question/20852004

* 协方差：
$S_{xy}=\frac{\sum_i^N {(x_i-\mu_x)(y_i - \mu_y)}}{n-1}$

* 相关系数：
$r = \frac{S_xy}{S_x*S_y}$, $S_x and S_y$分别表示x和y的标准差


对于nxm矩阵X，与nxm矩阵Y，协方差计算：
$C_{ij}=\frac{(x_i-\mu_x)(y_j - \mu_y)}{n-1}$
举例说明协方差计算，numpy.cov默认每一个行为一个变量，当$x_i$不是一个数字，而是一个一维向量时，那么$(x_i-mu_x)(x_j-mu_x)$就是向量的点积，向量按位乘的和。
numpy.cov求的是行与行的相关度，而通常dataset的行是不同的样本，列是feature，我们要求的是各个feature的相关度，也就是列与列的covariance，因此要用np.cov(x, rowvar=False)

```python
In [106]: x
Out[106]: 
array([[1, 2, 3],
       [4, 6, 8]])

In [107]: np.cov(x)
Out[107]: 
array([[ 1.,  2.],
       [ 2.,  4.]])

In [116]: np.cov(c[0],c[1])   # 可见结果一样，即求的是行与行的covariance
Out[116]: 
array([[ 1.,  2.],
       [ 2.,  4.]])

In [126]: np.cov(c, rowvar=False)
Out[126]: 
array([[  4.5,   6. ,   7.5],
       [  6. ,   8. ,  10. ],
       [  7.5,  10. ,  12.5]])

```

np.cov(x,y,rowvar=False)得到的结果是[[C_xx, Cyx], [C_xy, C_yy]]所组成的总协方差矩阵


相关性矩阵的计算与协方差类似，不过是将协方差矩阵的每一项，除以对应的xi yi的标准差之积。


## 回归算法

回归算法有很多：http://blog.jobbole.com/90021/



## [并行计算](http://blog.csdn.net/cloudeagle_bupt/article/details/19204349)
* Map-Reduce: 进程间通信都是通过文件进行，IO过多，不太适用于SVD这种任务。但容错性好
* MPI：线程级的粒度
* OpenMP：进程级的粒度




### 交叉熵与相对熵
https://www.zhihu.com/question/41252833

可用于作为损失函数