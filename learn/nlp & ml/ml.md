

## 总纲

### 记录
* 在知乎上搜书，发现《数学之美》和南大周志华的《机器学习》
*

* 看《python进行自然语言处理》，都是nltk的接口函数
* 研究RNN

### 比较

推荐：http://blog.csdn.net/JasonDing1354/article/category/2367859/4

1)概率分布的方法-------------Bayes方法, GMMs 用于复杂分布建模

2)决策树的方法（C4.5）---属性具有明确含义时使用，一种经典的方法

3)近邻分类-----------------------简单的方法，如果样本有代表性，维数不高时好用

4)支撑向量机--------------------高维空间的小样本学习

5)Boosting算法-----------------大量训练样本下可以取得好的效果，速度很快

6)人工神经网络ANN----------大量训练样本下可以取得好的效果，速度较慢



### 能解决的问题


所有问题上神经网络都更优吗？有需要用到传统算法的时候吗？
http://blog.csdn.net/u010167269/article/details/52642562
* 数据集比较小时，用深度学习，会过拟合
* 深度学习是 data driven 的，需要大量的数据，而传统的机器学习算法通畅不需要；
* 深度学习本质上可以看作一个特征学习器，在无需另构特征情况下，传统的机器学习算法已经能够胜任日常的任务；
* 如无必要，勿增实体。能够简单的模型解决的，不必要上深度学习算法，杀鸡焉用牛刀？


### 概念
神经网络
浅层网络：只有一个隐藏层
全连接神经网络：
CNN
RNN：
非凸优化问题：


### math
牛顿法：http://blog.csdn.net/ubunfans/article/details/41520047
Hessian矩阵：海森矩阵为函数对自变量的二次微分
Jacobian矩阵：雅克比矩阵为函数对各自变量的一阶导数

### 问题
CNN RNN的原理概念很清楚，最关键的是真正写tensorflow程序时，各种参数、结构是什么意思？
如CNN depth， RNN cell，state等

### blog
http://colah.github.io/
资源索引：https://zhuanlan.zhihu.com/p/26004118
论文：Neural Networks：Tricks of the Trade
推荐系统：http://www.tuicool.com/articles/emyMFvi
腾讯广告系统：http://www.tuicool.com/articles/eMf2iiu



### 机构
OpenAI

### 名词
stem：词干提取









## 卷积神经网络
http://blog.csdn.net/taigw/article/details/50534376
http://www.tuicool.com/articles/a6bqA3
https://zhuanlan.zhihu.com/p/25754846

### 卷积层
在由全连接神经网络处理之前。利用卷积核对图像进行卷积操作，提取出图像的某种特征。而卷积核本身则作为卷积层存在，其参数由神经网络学习得到（而不是用现成的卷积核，如模糊filter的卷积核）。卷积的结果要用激活函数激活，如Relu，之后再由池化层处理

* 深度(Depth)： 深度就是卷积操作中用到的滤波器个数。深度为n就是，将一副图像卷积后，得到n副图像（也叫n个特征）
* 步幅(Stride)：步幅是每次滑过的像素数。当Stride=1的时候就是逐个像素地滑动。当Stride=2的时候每次就会滑过2个像素。步幅越大，特征映射越小。
* 补零(Zero-padding)：有时候在输入矩阵的边缘填补一圈0会很方便，这样我们就可以对图像矩阵的边缘像素也施加滤波器。补零的好处是让我们可以控制特征映射的尺寸。补零也叫宽卷积，不补零就叫窄卷积。

RGB多通道如何卷积：https://www.zhihu.com/question/28832761，卷积核并不仅仅是二维的，RGB输入（灰度是二维的）是三维的，kernel也是三维的。**也可以把RGB图像理解为Depth为3的输入**

![图片](http://img1.tuicool.com/ABFRbeR.png!web)

n张图像的处理，只用理解其中一张图像处理，所有的图像在处理上都是一样的，仅仅是参数更新上，因为loss是考虑到所有的输入图像，因此会与n张图像都相关。

### 池化层
池化层通常紧随卷积层之后使用，其作用是简化卷积层的输出。如24*24的图像，将每2*2区域内的最大值保留，得到12*12的图像，这就是max-pooling技术，主要是简化数据用

在这个结构中，这样理解：卷积层和池化层学习输入图像中的局部空间结构，而后面的全连接层的作用是在一个更加抽象的层次上学习，包含了整个图像中的更多的全局的信息。

### dropout层
解决过拟合问题，http://blog.csdn.net/stdcoutzyx/article/details/49022443

alphaGo：参考论文：David Silver, et al. “Mastering the game of Go with deep neural networks and tree search.” Nature doi:10.1038/nature16961. 
相关链接：http://www.guokr.com/article/441144/



CNN系列专栏：https://zhuanlan.zhihu.com/p/22464594


##算法总结
传统算法
* 专家系统：基于规则，大量if else 正则表达式等
* 统计机器学习：最近邻 决策树 贝叶斯 随机森林。需先进行特征工程（业务或领域专家），费时费力
神经网络算法：CNN RNN 等

监督式学习：线性回归、逻辑回归、决策树
非监督式学习：Apriori算法、k-Means算法
半监督式学习：图论推理算法（Graph Inference）、拉普拉斯支持向量机（Laplacian SVM.）
强化学习：Q-Learning以及时间差学习（Temporal difference learning）


决策树：易于解释，但不适合处理特征值在决定合适标签过程中相互影响的情况
朴素贝叶斯：假定特征是独立的。如果处理特征有相关性的数据时，会导致权重叠加，会有问题


### 回归算法
* 最小二乘法（Ordinary Least Square）
* 逻辑回归（Logistic Regression）
* 线性回归（linear Regression）
* 逐步式回归（Stepwise Regression）
* 岭回归（Ridge Regression）

### DecisionTree
* 分类及回归树（Classification And Regression Tree， CART）
* ID3 (Iterative Dichotomiser 3)
* C4.5
* Decision Stump
* 随机森林（Random Forest）：是基于决策树的集成算法

构造一颗决策树需要解决四个问题：
* 选择什么属性进行分裂？如何给定分裂点的临界值？
* 如何评估分类的效果？
* 分裂到什么时候停止？
* 每个叶子节点都有一个对应的分类结果，怎么给？

http://www.tuicool.com/articles/FNriY3Q


### 贝叶斯方法
* 朴素贝叶斯算法
* 平均单依赖估计

### 基于核的方法
基于核的算法把输入数据映射到一个高阶的向量空间， 在这些高阶向量空间里， 有些分类或者回归问题能够更容易的解决
* 支持向量机（Support Vector Machine， SVM）
* 线性判别分析（Linear Discriminate Analysis ，LDA)：http://blog.jobbole.com/88195/

SVM资料，按顺序看[说明部分1](http://www.tuicool.com/articles/FjaaMb)；[推导部分2](http://www.tuicool.com/articles/uU7jUvy)， 关键点：
* SVM从线性可分情况下的分类面发展而来，其求解转化为一个约束优化问题
    * 拉格朗日乘子法：将约束条件融入目标函数，来求解
    * 拉格朗日后，转化为极值问题，通常求导后代入即可解决
* 对于线性不可分时？低维空间不可分，映射到高维空间往往就线性可分
* 如何映射？无系统性方法，靠经验猜。核方法就是描述这种低维——高位映射的方法
* 上述硬间隔SVM极易受少数点控制，对于噪声或离群点无法处理？加入松弛变量

系列博客：http://blog.csdn.net/jasonding1354/article/details/36220923

### 特征转换
* PCA、LDA：线性变换，通常是降维
* SVM的kernel：非线性变换，通常是升维


### 人工神经网络
* 感知器
* 自组织映射（Self-Organizing Map, SOM）


### 聚类算法
* k-Means算法
* 期望最大化算法（Expectation Maximization， EM）


### 关联规则学习
* Apriori算法
* Eclat算法

### 深度学习
* Deep Belief Networks（DBN）
* 卷积网络（Convolutional Network）
* 循环网络（RNN）

超参数选择：
书：Neural Networks：Tricks of the Trade
LeCun在1998年的论文《Efficient BackProp》
Bengio在2012年的论文《Practical recommendations for gradient-based training of deep 
architectures》,给出了一些建议，包括梯度下降、选取超参数的详细细节。

* 学习速率：根据training cost下降的情况调整学习速率；同时学习过程中可以动态改变学习速率，如到第20 epoch时从0.2降低到0.02
* Early Stopping：何时停止训练？当validation accuracy不再提高，则停止；正确的做法是，在训练的过程中，记录最佳的validation accuracy，当连续10次epoch（或者更多次）没达到最佳accuracy时，你可以认为“不再提高”（no-improvement-in-n），此时使用early stopping
* 可变学习速率：一个简单有效的做法就是，当validation accuracy满足 no-improvement-in-n规则时，本来我们是要early stopping的，但是我们可以不stop，而是让learning rate减半，之后让程序继续跑。下一次validation accuracy又满足no-improvement-in-n规则时，我们同样再将learning rate减半（此时变为原始learni rate的四分之一）…继续这个过程，直到learning rate变为原来的1/1024再终止程序。
* 正则项系数（regularization parameter, λ）：建议一开始将正则项系数λ设置为0，先确定一个比较好的learning rate。然后固定该learning rate，给λ一个值（比如1.0），然后根据validation accuracy，将λ增大或者减小10倍（增减10倍是粗调节，当你确定了λ的合适的数量级后，比如λ = 0.01,再进一步地细调节，比如调节为0.02，0.03，0.009之类。）

### batch size：http://blog.csdn.net/fuwenyan/article/details/53914371
* mini-batch size：如果不分批次，一次训练耗时过久，参数更新太慢。将n个sample分为m*k=n, k个批次，每个batch训练使用m个sample，计算和迭代一次的速度会更快。
* full-batch learning：如果数据集比较小，完全可以采用全数据集 （ Full Batch Learning ）的形式，全数据集确定的方向能够更好地代表样本总体
* Online learning：与full-batch learning相对的另一个极端，Batch_size=1，每次只训练一个样本。

### epoch
对所有训练数据完成一次计算称为一次epoch，举例说明
对一个样本数为n，每个样本的feature数为m的训练数据集，表示为n*m的矩阵
* full-batch size，那么每次训练输入都是n*m矩阵，一次反向传播更新权重，称为一次epoch
* batch-size=k，那么每次训练输入的是k*m矩阵，训练集分为了n/k个batch，一次反向传播更新权重，针对的是一个batch，那么完成所有训练集的处理，需要n/k次反向传播，称为一次epoch

通常以一次反向传播称一次step，一个step处理一个batch的数据，v次step后（v和epoch可以无关），可以做一次validation，判断收敛情况。

### 降低维度算法
* LDA
* 主成份分析（Principle Component Analysis， PCA）
* 偏最小二乘回归（Partial Least Square Regression，PLS）

### 组合算法

组合算法中，一类是Bagging（装袋），另一类是Boosting（提升），随机森林便是Bagging中的代表。

### 随机森林
* http://blog.jobbole.com/99536/
* “随机”是其核心灵魂，“森林”只是一种简单的组合方式而已
* 数据抽样：每棵树都随机在原有数据上有放回的抽样，保证每棵树学习的数据侧重点不同
* 构建决策：在每次决策分支时，也需要加入随机性，每次只随机取其中的几个来判断决策条件
    * 在每次决策分支时，也需要加入随机性，假设数据有20个特征（属性），每次只随机取其中的几个来判断决策条件
    * 还有一种方式，就是在最好的几个（依然可以指定sqrt与log2)分裂属性中随机选择一个来进行分裂
* 组合：每个基算法单独预测，最后的结论由全部基算法进行投票（用于分类问题）或者求平均（包括加权平均，用于回归问题）

### 组合算法算法-boosting

Boosting分类器属于集成学习模型，它基本思想是把成百上千个分类准确率较低的树模型组合起来，成为一个准确率很高的模型。
* adaboost：一种boosting方法
关键点——更新权重：计算loss时，sum(y-y_pred)*w，每个sample的loss之和为总权重，对于分类错误的sample，将其loss的权重加大，则其对总loss影响就会更大
* xgboost：eXtreme Gradient Boosting，是一个开源库
* GBDT：Gradient Boosting Decision Tree，一种boosting方法，以分类树和回归树为基本分类器，http://blog.jobbole.com/88193/


### 梯度下降
神经网络的梯度下降也有很多中：
* 动量梯度下降(QuickProp)
* Nesterov 加速动量(NAG)梯度下降
* 自适应梯度算法(AdaGrad)
* 弹性反向传播(RProp)
* 均方根反向传播(RMSProp)


### 激活函数
激活函数也有很多：
* simoid
* Relu
* tanh

### Loss函数


### 优化函数



## NLP
**相对于图像和语音来说，文字已经是高度抽象的概念了，因此对文本分析并不需要太深的网络结构。**


## 应用
算法主要包括分类、聚类、预测问题，其他问题需要先转换到这三类问题，才能解决，如：
* 分词转换为对词的分类，所有词对应到BIOE标签，确定了BIOE标签也就完成了分词
* 词性标注，典型的分类问题


### bias-variance tradeoff
偏差度量了学习算法的期望预测与真实结果的偏离程度，即刻画了学习算法本身的拟合能力；方差度量了同样大小的训练集的变动所导致的学习性能的变化，即刻画了数据扰动所造成的影响；噪声则表达了学习问题本省的难度。偏差－方差分解说明，泛化能力是由学习算法的能力、数据的充分性以及学习任务本身的难度所共同决定的，给定学习任务，为了取得好的泛化性能，需使偏差较小，即能够充分拟合数据，并使方差较小，使数据扰动产生的影响最小。
