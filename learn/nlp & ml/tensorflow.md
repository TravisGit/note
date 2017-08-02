

官方教程：http://www.tensorfly.cn/tfdoc/how_tos/variables.html

github：
* 库：
* 示例model：https://github.com/tensorflow/models
* tutorials源码：https://github.com/tensorflow/models/tree/master/tutorials
* tutorials minst 源码：
* https://github.com/zzw922cn/Automatic_Speech_Recognition
* https://www.tensorflow.org/extras/candidate_sampling.pdf
* 持续集成：https://ci.tensorflow.org/view/Nightly/job/nightly-android/


## 基本概念

graph：表示计算任务
session：表示执行程序的上下文
tensor：表示数据，python中返回的tensor是numpy ndarray对象
variable：维护状态的变量，如训练的结果weights & biases，可存储到磁盘
feed fetch：可为任意的操作赋值或从其中取回数据

TensorFlow 程序通常被组织成一个构建阶段和一个执行阶段. 
在构建阶段, op 的执行步骤 被描述成一个图. 
在执行阶段, 使用会话执行执行图中的 op.

**说白了，就是python太慢，tensorflow框架是使用c++实现的，所以要在python中使用，对于所有的变量和所有的操作，都保持一个handle在python环境中，然后在session中去运算**

**换一种理解方式，tensorflow就是一个解释器，将python程序解释为对应的tensorflow形式的C++程序，然后运行。**


```python
import tensorflow as tf

# 创建一个常量 op, 产生一个 1x2 矩阵. 这个 op 被作为一个节点
# 加到默认图中.
#
# 构造器的返回值代表该常量 op 的返回值.
matrix1 = tf.constant([[3., 3.]])

# 创建另外一个常量 op, 产生一个 2x1 矩阵.
matrix2 = tf.constant([[2.],[2.]])

# 创建一个矩阵乘法 matmul op , 把 'matrix1' 和 'matrix2' 作为输入.
# 返回值 'product' 代表矩阵乘法的结果.
product = tf.matmul(matrix1, matrix2)

# 启动默认图.
sess = tf.Session()

# 调用 sess 的 'run()' 方法来执行矩阵乘法 op, 传入 'product' 作为该方法的参数. 
# 上面提到, 'product' 代表了矩阵乘法 op 的输出, 传入它是向方法表明, 我们希望取回
# 矩阵乘法 op 的输出.
#
# 整个执行过程是自动化的, 会话负责传递 op 所需的全部输入. op 通常是并发执行的.
# 
# 函数调用 'run(product)' 触发了图中三个 op (两个常量 op 和一个矩阵乘法 op) 的执行.
#
# 返回值 'result' 是一个 numpy `ndarray` 对象.
result = sess.run(product)
print result
# ==> [[ 12.]]

# 任务完成, 关闭会话.
sess.close()
```

Session 对象在使用完后需要关闭以释放资源. 除了显式调用 close 外, 也可以使用 "with" 代码块 来自动完成关闭动作.

```python
with tf.Session() as sess:
  result = sess.run([product])
  print result
```

上述的例子中使用的是常量，tensorflow中当然与要有变量，显然的，变量在使用前需要初始化

```python
# Create two variables.
weights = tf.Variable(tf.random_normal([784, 200], stddev=0.35),
                      name="weights")
biases = tf.Variable(tf.zeros([200]), name="biases")
...
# Add an op to initialize the variables.
init_op = tf.initialize_all_variables()

# Later, when launching the model
with tf.Session() as sess:
  # Run the init operation.
  sess.run(init_op)
  ...
  # Use the model
  ...
```



## 遇到问题
* 安装成功后，在import tensorflow时报错：
```
ImportError: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.17' not found (required by /usr/local/lib/python2.7/dist-packages/tensorflow/python/_pywrap_tensorflow.so)
```

[stackoverflow](https://stackoverflow.com/questions/33655731/error-while-importing-tensorflow-in-python2-7-in-ubuntu-12-04-glibc-2-17-not-f)上有解决方案：
```bash
$ cd /home/jty/neffos/libc6_2.17-0ubuntu5_amd64/
$ wget http://launchpadlibrarian.net/137699828/libc6_2.17-0ubuntu5_amd64.deb        #网络不好可能导致下载内容为0字节，后面解压失败
$ wget http://launchpadlibrarian.net/137699829/libc6-dev_2.17-0ubuntu5_amd64.deb
$ mkdir libc6_2.17
$ cd libc6_2.17
$ ar p ../libc6_2.17-0ubuntu5_amd64.deb data.tar.gz | tar zx                        #这里sudo也执行不了，sudo -s切换到root执行
$ ar p ../libc6-dev_2.17-0ubuntu5_amd64.deb data.tar.gz | tar zx
$ cd -
$ LD_LIBRARY_PATH=/home/jty/neffos/libc6_2.17-0ubuntu5_amd64/lib/x86_64-linux-gnu/ /home/jty/neffos/libc6_2.17-0ubuntu5_amd64/lib/x86_64-linux-gnu/ld-2.17.so `which python`    #打开python交互式窗口
```

## 文本分类
CNN：
* 每个词被表示为1*k的向量
* 每个句子被表示为m*k矩阵，m为句子长度
* 输入：m*k矩阵

seq2seq
http://suriyadeepan.github.io/2016-06-28-easy-seq2seq/
