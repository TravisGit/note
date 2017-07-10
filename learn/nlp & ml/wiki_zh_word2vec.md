

## 基于wiki中文预料，建立word2vec
参考：http://www.52nlp.cn/%E4%B8%AD%E8%8B%B1%E6%96%87%E7%BB%B4%E5%9F%BA%E7%99%BE%E7%A7%91%E8%AF%AD%E6%96%99%E4%B8%8A%E7%9A%84word2vec%E5%AE%9E%E9%AA%8C/comment-page-1

wiki简体中文预料获取：http://licstar.net/archives/262

### 0. 环境
* python3.5
* sudo pip install numpy gensim

### 1. 下载wiki中文预料
中文数据的下载地址是：<https://dumps.wikimedia.org/zhwiki/latest/zhwiki-latest-pages-articles.xml.bz2>


### 2. 数据预处理——解压数据

将数据解压，使用的脚本：
<https://github.com/panyang/Wikipedia_Word2vec/blob/master/v1/process_wiki.py>

执行命令：

```
python process_wiki.py zhwiki-latest-pages-articles.xml.bz2 wiki.zh.text

```

### 3. 数据预处理——繁简转换
* 下载opencc工具：
<https://launchpad.net/ubuntu/+source/opencc/1.0.4-5>
* 执行安装命令：
```bash
sudo apt-get install doxygen
cd ~ && mkdir tmp
cd tmp && tar -zxvf xxxx.tar.gz
cd opencc
make && sudo make install
```
* opencc运行繁简转换：
```
opencc -i wiki_zh_text -o wiki.zh.text.jian -c zht2zhs.ini

```
* iconv去除非utf-8字符
```
iconv -c -t UTF-8 < wiki.zh.text.jian > wiki.zh.text.jian.utf8

```

### 4. 分词
* 使用jieba分词，首先安装jieba分词：
```
pip install jieba

```
* 编写分词程序：
```python
#!/usr/bin/env python
# coding=utf-8
import jieba.posseg as pseg
import jieba
import sys

def seg_wiki_corpus(inFile, outFile):

    with open(inFile, 'r') as input:
        with open(outFile, 'w') as output:
            line = input.readline()
            while line:
                output.write(" ".join(jieba.cut(line)))
                line = input.readline()

if __name__ == '__main__':
    inFile, outFile= sys.argv[1:4]
    seg_wiki_corpus(inFile , outFile)


```
* 运行分词程序：耗时半小时
```bash
python jieba_seg.py wiki.zh.text.jian.utf8 wiki.zh.text.jian.seg

```
### 5. word2vec训练
* 源码：https://github.com/panyang/Wikipedia_Word2vec/blob/master/v1/train_word2vec_model.py

* 执行命令：耗时半小时
```bash
python train_word2vec_model.py wiki.zh.text.jian.seg wiki.zh.text.model wiki.zh.text.vector

```


### 6. 测试效果

```python
>>> import gensim
>>> model = gensim.models.Word2Vec.load("wiki.zh.text.model")
>>> model.most_similar(u"足球")
[('国际足球', 0.5646348595619202), ('足球运动', 0.5382945537567139), ('篮球', 0.5253003835678101), ('国家足球队', 0.5164145231246948), ('足球队', 0.5123095512390137), ('足球比赛', 0.4986734390258789), ('冰球', 0.485721230506897), ('板球', 0.485215425491333), ('足球联赛', 0.48365041613578796), ('排球', 0.4798141121864319)]
>>> model.most_similar(u"男人")
[('女人', 0.7512872219085693), ('家伙', 0.6292130947113037), ('有钱人', 0.5090193748474121), ('傻瓜', 0.5016800165176392), ('小伙子', 0.49857598543167114), ('女孩', 0.4978784918785095), ('女孩子', 0.49233102798461914), ('男孩', 0.48870906233787537), ('老公', 0.4867798089981079), ('小孩子', 0.4827907085418701)]
>>> model.similarity(u"自动化", u"计算机")
0.54878041966003899

```

