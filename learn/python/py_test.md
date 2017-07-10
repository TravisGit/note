

gil_single.py
```python
from threading import Thread
import time

def my_counter():
    i = 0
    for _ in range(10000000):
        i = i + 1
    return True

def main():
    thread_array = {}
    start_time = time.time()
    for tid in range(2):
        t = Thread(target=my_counter)
        t.start()
        t.join()
        print(tid)
    end_time = time.time()
    print("Total time: {}".format(end_time - start_time))

if __name__ == '__main__':
    main()

```

gil_multi.py
```python
from threading import Thread
import time

def my_counter():
    i = 0
    for _ in range(10000000):
        i = i + 1
    return True

def main():
    thread_array = {}
    start_time = time.time()
    for tid in range(2):
        t = Thread(target=my_counter)
        t.start()
        thread_array[tid] = t
    for i in range(2):
        thread_array[i].join()
        print(i)
    end_time = time.time()
    print("Total time: {}".format(end_time - start_time))

if __name__ == '__main__':
    main()

```

上述程序耗时
x | gil_single.py | gil_multi.py
---------|----------|---------
 python2.7 range | 1.28 | 1.86
 python2.7 xrange | 0.60 | 1.09
 python3 range| 0.99 | 0.98

对计算密集型任务
 * xrange比range性能高
 * python3多线程与单线程性能相近
 * python2多线程性能较差


## doctest
https://docs.python.org/dev/library/doctest.html

注意,**>>>与指令之间是有空格的**：
```python
"""
>>> x = 1
>>> x
1
"""

```

## unitest


## nosetest
nosetest基于unitest，更加易用
安装：pip install nose

* 直接在当前目录执行nosetests，会自动检测所有可测试的py文件
* 默认不测试doctest，可在当前目录配置setup.cfg打开doctest
```
[nosetests]
verbosity=3
with-doctest=1
exclude=train

```
* 其他更精细化测试方法，测试某个文件、某个目录、某个方法：
```
nosetests test.module
nosetests another.test:TestCase.test_method
nosetests a.test:TestCase
nosetests /path/to/test/file.py:test_function
```

注意nosetests的自动查找匹配可以通过如下参数配置修改：

```
-m=REGEX, --match=REGEX, --testmatch=REGEX
    Files, directories, function names, and class names that match this regular expression are considered tests.  Default: (?:^|[b_./-])[Tt]est [NOSE_TESTMATCH]
-e=REGEX, --exclude=REGEX
    Don't run tests that match regular expression [NOSE_EXCLUDE]

-i=REGEX, --include=REGEX
    This regular expression will be applied to files, directories, function names, and class names for a chance to include additional tests that  do  not  match  TESTMATCH.   Specify  this
    option multiple times to add more regular expressions [NOSE_INCLUDE]
```