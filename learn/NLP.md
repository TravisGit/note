

# 数学之美

## 文字和语言 vs 数字和信息

## PageRank

网页质量衡量

## TF-IDF
相关性衡量
针对某个查询，如何找到最相关网页？

TF-Term Frequency：单文本词频，比如网页一共1000个词，其中“原子能”，“的”分别出现了2次、35次，则它们的词频分别是0.002、0.035

IDF-Inverse Document Frequency：逆文本频率指数，关键词w在$D_w$个网页中出现个，总网页数为D，则w的IDF为$log(\frac{D_w}{D})$


通常，词可以分为三类，“原子能/的/应用”中：
“原子能”：是非常专业的词，与相关性关系最大
“的”：基本毫无意义
“应用”：通用词，相关性关系不大

Stop-Word：停止词，类似于“是”，“的”，“地”等，github中可以找到中日韩等各类语言的停止词库，停止词直接过滤掉就好，完全不用考虑也不参与计算。


只考虑TF的情况下：
如果一个查询包含N个关键词w1,w2,...,wn，它们在一个特定网页中的词频分别是TF1,TF2,...,TFn。那么，这个查询和该网页的相关性为：
$$TF1+TF2+...+TF_N$$

此时如果一个查询中“应用”的词频很高，但“原子能”的词频很低，那么计算出来的相关性很高，这显然是不合理的。因此我们要能够识别出哪些是专业的、重要的词，哪些是通用词，这里讲IDF考虑进来，相关性计算公式为：

$$TF_1*IDF_1 + TF_2*IDF_2 + ... + TF_N*IDF_N$$

### 高级
IDF的概念其实是一个特定条件下关键词的概率分布的交叉熵



## Bloom Filter
布隆过滤器——解决的问题：
如何确定一个元素是否在集合中出现过？
对应到具体问题：
如何确定一个邮件是否在垃圾邮件列表中出现过？

通常判断元素是否在集合中，最常用的数据结构是散列表。假设一个网址对应到信息指纹（hash）为8bytes，那么一亿个地址需要8亿字节，考虑到散列表存储效率一般只有50%，也就是1.6GB内存。全世界垃圾邮件地址有几十亿垃圾邮件地址，那么需要上百GB内存。

Bloom Filter只需要散列表的1/4(或1/8，与误判率有关)大小，就可以解决同样的问题。

* 建立一个m比特长的位向量，初始化全为0
* 对每一个地址，使用8个不同随机数产生器，产生8个信息指纹
* 再使用一个随机数产生器，将8个信息指纹分别映射到1~m中的自然数$g_1,g_2,...,g_8$
* 将$g_1,g_2,...,g_8$对应位置1

判断Y是否位垃圾邮件，则同样处理Y得到8个自然数，查看Bloom Filter的位向量中，对应位是否全为1，如果是垃圾邮件，则对应位一定为1

Bloom Filter不会漏判，但会误判，误判的问题可以通过一个简短的白名单列表解决。下面我们来看漏判概率。

假设Bloom Filter有m位，存储n个元素，每个元素对应k个信息指纹：
* 每插入一个元素，Bloom Filter中任意一位仍然是0的概率为$(1-1/m)^k$
* 则n个元素全插入，某一位仍然未被置1的概率为$(1-1/m)^{kn}$
* 反过来，n个元素插入后，某一个被置1的概率为$1- (1-1/m)^{kn}$
* 一个不在n中的元素，其所有位被置1的概率为$(1- (1-1/m)^{kn})^k$
* 可以近似为$(1-e^{-kn/m})^k$

显然随着k和n单调递增，随着m单调递减。最关键的是m/n只要足够大，BloomFilter误判率就会很小，当m/n为32，k=8时，误判率只有5.73e-06

原本1亿地址，散列表需要16亿bytes，16*8亿bit。Bloom Filter需要1*32亿bit，为散列表的1/4

核心思想：位图、多位表示

## Viterbi Algorithm
维特比算法，是现代数字通信中最常用的算法，也是自然语言处理采用的解码算法。

维特比算法，是一个特殊但应用最广的动态规划算法；利用动态规划，可以解决任何一个图中的最短路径问题。

凡是使用隐含马尔科夫模型的问题（语音识别、机器翻译、拼音转汉字、分词等）都可以用它来解决


## 概率分布

[如何在Python中实现这五类强大的概率分布](http://python.jobbole.com/81321/)

* 二项分布
* 泊松分布
* 正态分布
* β分布
* 指数分布

## 词向量word2vec
http://licstar.net/archives/328

* one-hot representation：[0 0 0 1 0 0 ...]，如果采用系数矩阵存储，直接给每个词分配一个数字ID即可（比如做个hash）；维度过高；词和词之间是孤立的，无法判定相关性
* Distributed Representation：[0.792, −0.177, −0.107, 0.109, −0.542, …]，低维实数向量（通常50维或100维）；也叫“Word Representation”或“Word Embedding”

google搜的博客讲的清楚：http://mccormickml.com
* CBOW模型：http://mccormickml.com/assets/word2vec/Alex_Minnaar_Word2Vec_Tutorial_Part_II_The_Continuous_Bag-of-Words_Model.pdf
* Skip-Gram模型：http://mccormickml.com/2016/04/19/word2vec-tutorial-the-skip-gram-model/
* negative-sampling，降低训练参数的数量：http://mccormickml.com/2017/01/11/word2vec-tutorial-part-2-negative-sampling/

关键点：
* 最终的词向量，就是权重w
* 如何生成训练样本。窗口技术，如窗口是2，则"the man saw a cat run in a park"中，context为the man a cat，输出预测词为saw
* negative-sampling，如何选取，将所有词分布在0~1的线段上，随机选取：http://blog.csdn.net/itplus/article/details/37998797
* 输出层使用了huffman树

资料整理，如下两个系列，任一个都可以看懂：
* 英文：http://mccormickml.com
* 中文：http://blog.csdn.net/itplus/article/details/37998797



google code相关源码：https://code.google.com/archive/p/word2vec/


网课：
* stanford课件：https://cs224d.stanford.edu/lecture_notes/
* 52nlp上有stanford课件中文版：http://www.52nlp.cn/%E6%96%AF%E5%9D%A6%E7%A6%8F%E5%A4%A7%E5%AD%A6%E6%B7%B1%E5%BA%A6%E5%AD%A6%E4%B9%A0%E4%B8%8E%E8%87%AA%E7%84%B6%E8%AF%AD%E8%A8%80%E5%A4%84%E7%90%86%E7%AC%AC%E4%BA%8C%E8%AE%B2%E8%AF%8D%E5%90%91%E9%87%8F

演化
* one-shot词向量，太过稀疏
* word-document matrix：词袋模式，每个文档表示为一个向量v，语料库中所有文档组成m*v规模矩阵，进行svd分解，得到词向量。缺点规模收M影响，可能很大
* window based co-occurrence Matrix（共现矩阵）：滑动窗口查看所有词的相关关系，建立v*v矩阵，进行svd分解，取前k个特征，得到词向量。新词加入则矩阵规模变化，不够灵活
* 



### RNN
RNN的基本算法
* $x_t$是 t 时刻的输入，可以是单词或者句子的One-hot编码；
* 隐藏层$s_t=f(Ux_t+Ws_{(t-1)})$，通常函数f会选择tanh或者ReLU，$s_t−1$是前一时刻的隐藏状态，当t=0时，s−1通常被初始化为0向量；
* 输出$O_t=Softmax(Vs_t)$

* The Unreasonable Effectiveness of Recurrent Neural Networks:  http://karpathy.github.io/2015/05/21/rnn-effectiveness/

* char-RNN:http://jaybeka.github.io/2016/05/18/rnn-intuition-practice/
* 双向RNN
* LSTM: http://colah.github.io/posts/2015-08-Understanding-LSTMs/
* LSTM:http://www.jianshu.com/p/f3bde26febed
* seq2seq
* RecursiveNN：http://www.jianshu.com/p/403665b55cd4，输入是句子，输出是一个parser树结构


### 超参数

激活函数选取：http://blog.csdn.net/suixinsuiyuan33/article/details/69062894?locationNum=4&fps=1





