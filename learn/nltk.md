

sudo apt-get install sqlite3
sudo apt-get install libsqlite3-dev

从源码重编译python3.5
./configure --enable-loadable-sqlite-extensions && make && sudo make install

pip install nltk

在python命令行中执行：
import nltk
nltk.download("all-corpora")        下载测试用语料库数据


# 用python进行自然语言处理
可以作为nltk的api工具书查阅


##

搭配和双连词
>>> from nltk.util import bigrams
>>> list(bigrams([1,2,3,4,5]))
[(1, 2), (2, 3), (3, 4), (4, 5)]

1.5 自动理解自然语言

词意消歧：特定上下文的词被赋予什么意思
the cup **by** the stove
submit **by** friday

指代消解：检测主语和动词的宾语，代词到底指的是什么
The thieves stole the paintings. **They** were sold
The thieves stole the paintings. **They** were caught

自动生成语言：自动问答和机器翻译

人机对话系统

token：指文本中给定词的特定出现，len(text)
type：指词作为一个特定序列字母的唯一形式，len(set(text))

## 获得文本预料和词汇资源

### 获取文本与语料库
Gutenberg语料库
```
nltk.corpus.gutenberg.fileids()  //列出所有
emma = nltk.corpus.gutenberg.words('austen-emma.txt') //加载

```
网络和聊天文本：nltk.corpus.webtext.fileids()，包含有firefox论坛内容，加勒比海盗剧本等

布朗语料库：第一个百万词级的英语电子语料库。包含500个不同来源文本，并分为多类
```
from nltk.corpus import brown
brown.categories()

```
路透社语料库：包括90个主题10788个新闻文档
```
from nltk.corpus import reuters
reuters.fileids()
reuters.categories()

```
udhr是超过300中语言的世界人权宣言。

语料库的结构
* 最简单的一种没有任何结构，仅仅是文本集合
* 通常，文本会按照来源、作者、语言、主题等分类，有些类别中的文本可能重叠

载入自己的语料库：nltk.corpus.PlaintextCorpusReader

### 词典资源


## 第5章 分类和标注词汇
1. 什么是词汇分类，在自然语言处理中它们是如何使用？
2. 一个好的存储词汇和它们的分类的 Python 数据结构是什么？
3. 我们如何自动标注文本中词汇的词类？
一路上，我们将介绍 NLP 的一些基本技术，包括序列标注、N-gram 模型、回退和评估。
这些技术在许多方面都很有用，标注为我们提供了一个表示它们的简单的上下文。我们还将
看到**标注为何是典型的 NLP 流水线中继分词之后的第二个步骤**。


## 常用
* 基本数据结构是nltk.text.Text，可用nltk.Text()得到
* text1.concordance：类似于grep
* gutenberg.raw：字符集（原始文本，大字符串），words：词集（以词分割），sents：句子集
* FreqDist：频率分布，（词频统计，除了词频，也可用来统计长度等）
* ConditionalFreqDist：条件频率分布。是频率分布的集合，每个频率分布对应了不同的条件
* 停用词：from nltk.corpus import stopwords


