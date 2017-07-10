
<!-- TOC -->

- [爬虫](#爬虫)
    - [简介](#简介)
    - [实现方式](#实现方式)
    - [scrapy框架](#scrapy框架)
        - [资料](#资料)
        - [应用举例](#应用举例)
        - [解析器](#解析器)
        - [如何编写spider](#如何编写spider)
        - [如何设置停止条件](#如何设置停止条件)
        - [如何处理js信息](#如何处理js信息)
        - [如何配置多线程](#如何配置多线程)

<!-- /TOC -->

# 爬虫
本文的爬虫都是以python为例

所谓网络爬虫，就是一个在网上到处或定向抓取数据的程序，当然，这种说法不够专业，更专业的描述就是，抓取特定网站网页的HTML数据。抓取网页的一般方法是，定义一个入口页面，然后一般一个页面会有其他页面的URL，于是从当前页面获取到这些URL加入到爬虫的抓取队列中，然后进入到新页面后再递归的进行上述的操作，其实说来就跟**深度遍历或广度遍历**一样。

## 简介
爬虫的作用在于自动化从网络下载数据，一个完整的爬虫流程应该如下：

* 下载：依据起始url，获取网页内容
* 解析：解析网页内容，得到目标内容
* 存储：将解析后的信息存储


## 实现方式
1. 完全手动实现
    * 写python程序，完整的完成爬数据、解析html/xml

2. 利用框架，减轻一部分工作：
    * beautifulsoup：相当于轮子，主要是一个html/xml解析器，整个流程还要结合urllib或者request实现
    * scarpy：相当于车子，已经包含了爬区数据、解析、线程和管道管理等功能。开发者要做的主要是配置抓取的规则、解析的规则。由于自成体系，相对难以用到集成系统中。


## scrapy框架

scrapy是一个完整的爬虫框架，包含抓取、解析、线程调度等所有功能，其架构：

![scrapy-backbone](http://jason-images.qiniudn.com/@/python/scrapy/intro/scrapy_backbone.png)

从图中的绿色箭头看执行流程
1. 用户设置初始url
2. Scheduler将初始url交给Downloader进行下载
3. Downloader将下载完的内容交给Spider进行解析
4. Spider解析的结果又两种：
    * 一种是需要进一步抓取的链接，例如之前分析的“下一页”的链接，这些东西会被传回Scheduler
    * 另一种是需要保存的数据，它们则被送到Item Pipeline那里，那是对数据进行后期处理（详细分析、过滤、存储等）的地方


各模块作用：
* 引擎(Scrapy Engine)，用来处理整个系统的数据流处理，触发事务。
* 调度器(Scheduler)，用来接受引擎发过来的请求，压入队列中，并在引擎再次请求的时候返回。
* 下载器(Downloader)，用于下载网页内容，并将网页内容返回给蜘蛛。
* 蜘蛛(Spiders)，蜘蛛是主要干活的，用它来制订特定域名或网页的解析规则。编写用于分析response并提取item(即获取到的item)或额外跟进的URL的类。 每个spider负责处理一个特定(或一些)网站。
* 项目管道(Item Pipeline)，负责处理有蜘蛛从网页中抽取的项目，他的主要任务是清晰、验证和存储数据。当页面被蜘蛛解析后，将被发送到项目管道，并经过几个特定的次序处理数据。
* 下载器中间件(Downloader Middlewares)，位于Scrapy引擎和下载器之间的钩子框架，主要是处理Scrapy引擎与下载器之间的请求及响应。
* 蜘蛛中间件(Spider Middlewares)，介于Scrapy引擎和蜘蛛之间的钩子框架，主要工作是处理蜘蛛的响应输入和请求输出。
* 调度中间件(Scheduler Middlewares)，介于Scrapy引擎和调度之间的中间件，从Scrapy引擎发送到调度的请求和响应。

### 资料

如何使用scrapy来定制我们的爬虫，抓取我们想要的数据，网上有很多学习资料，这里简单列举：
* [Scrapy Tutorial](https://doc.scrapy.org/en/latest/intro/tutorial.html)：官网的入门教程，包含安装、demo、原理、常见问题等所有你想知道的一切
* [Github Examples](https://github.com/geekan/scrapy-examples)：包含qqnews、douban movie等常见资源的的爬虫实现，可以任意选取一个，修改成我们想要的爬虫
* [Scrapy](https://scrapy.org/)：官方网站

### 应用举例

**以爬取煎蛋妹子图为例**

1. 安装scrapy，就是常见的python包安装：
```
pip install scrapy

```
2. 使用全局命令，建立爬虫项目：
```
scrapy startproject jiandan

```
3. Scrapy的默认目录结构如下：
```
.
├── jiandan
│   ├── __init__.py
│   ├── items.py             #保存爬取数据的容器
│   ├── middlewares.py
│   ├── pipelines.py
│   ├── settings.py
│   └── spiders
│       └── __init__.py
└── scrapy.cfg

```
4. 创建Spider，会基于模板生成一个spider
```bash

➜  jiandan git:(master) ✗ scrapy genspider meizi http://jandan.net/ooxx
Created spider 'meizi' using template 'basic' in module:
  jiandan.spiders.meizi

```

5. 现在假设我们仅仅想爬所有图的链接，并打印，那么我们只需要修改spiders/meizi.py文件即可
```python
# -*- coding: utf-8 -*-
import scrapy


class MeiziSpider(scrapy.Spider):
    name = "meizi"
    allowed_domains = ["jandan.net"]
    start_urls = ['http://jandan.net/ooxx/']
    image_count = 0

    def parse(self, response):
        image_urls = response.css('img').xpath('@src').extract()
        next_page = response.xpath(
            '//a[contains(@title, "Older Comments")]/@href').extract_first()
        print(next_page)
        for href in image_urls:
            print("the %d image url:%s" % (MeiziSpider.image_count,href))
            MeiziSpider.image_count += 1

        yield scrapy.Request(next_page , callback=self.parse)

        pass

```
这里的关键是如何使用response.css response.xpath解析出想要的内容，方法参见后面章节


6. 执行scrapy crawl meizi，打印所有图片链接
```bash
➜  jiandan git:(master) ✗ scrapy crawl meizi 
#截取一部分结果
……
2017-07-08 01:36:47 [scrapy.core.engine] DEBUG: Crawled (200) <GET http://jandan.net/ooxx/page-163#comments> (referer: http://jandan.net/ooxx/page-164)
http://jandan.net/ooxx/page-162#comments
the 157 image url://ws3.sinaimg.cn/mw600/9f0e9ec6ly1fh7z0ta0wsj20r0100nme.jpg
the 158 image url://ws4.sinaimg.cn/mw600/9f0e9ec6ly1fh7z0g3wboj20u011idho.jpg
the 159 image url://wx1.sinaimg.cn/mw600/8c1483d3ly1fh7wm0sfgzj20j60oldiw.jpg
the 160 image url://wx2.sinaimg.cn/mw600/0062kotHgy1fh7ofoboajj31jk24
……

```

**到目前为止，我们仅仅修改了spider/meizi.py文件，就可以打印出所有meizi图链接**，这个过程中，我们并未看到pipeline、item等其他模块的作用，接下来我们尝试把图片保存到本地


7. 编写item.py，定义目标数据的类型（表示我们需要什么）：
```python
# -*- coding: utf-8 -*-

# Define here the models for your scraped items
#
# See documentation in:
# http://doc.scrapy.org/en/latest/topics/items.html

import scrapy


class MeizituItem(scrapy.Item):
    # define the fields for your item here like:
    url = scrapy.Field()
    name = scrapy.Field()
    tag = scrapy.Field()
    image_urls = scrapy.Field()     #实际只用到此field
    image = scrapy.Field()

    pass

```

8. 修改spider/meizi.py，另parse函数返回MeizituItem，从而可以被pipeline处理
```python
# -*- coding: utf-8 -*-
import scrapy
from ..items import MeizituItem
from ..pipelines import ImageDownloadPipeline


class MeiziSpider(scrapy.Spider):
    name = "meizi"
    allowed_domains = ["jandan.net"]
    start_urls = ['http://jandan.net/ooxx/']
    image_count = 0

    def parse(self, response):
        if  ImageDownloadPipeline.stop_count > 100:
            print("alread download more than 100 pictures, over")
            return
        image_urls = response.css('img').xpath('@src').extract()
        next_page = response.xpath(
            '//a[contains(@title, "Older Comments")]/@href').extract_first()
        print(next_page)
        for href in image_urls:
            print("the %d image url:%s" % (MeiziSpider.image_count,href))
            MeiziSpider.image_count += 1
        ##########################################
        # 这里返回item，给pipeline处理！！！
        yield MeizituItem(image_urls=image_urls)
        ##########################################

        yield scrapy.Request(next_page , callback=self.parse)

        pass

```

9. 填充pipeline.py文件，用于内容的下载。spider将image_url封装到MeizituItem传递到pipeline，由pipeline完成下载图片存储到本地的操作，至于如何下载，方法有很多，这里借助requests库：

```python
# -*- coding: utf-8 -*-

# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: http://doc.scrapy.org/en/latest/topics/item-pipeline.html
import os
import re
import urllib
import socket
socket.setdefaulttimeout(0.1)

class ImageDownloadPipeline(object):
    stop_count = 0

    def process_item(self, item, spider):
        if ImageDownloadPipeline.stop_count > 100:
            return item

        if 'image_urls' in item:
            # 指定下载后保存的目录download
            dir_path = '%s/%s' % ("download", spider.name)

            if not os.path.exists(dir_path):
                os.makedirs(dir_path)
            for image_url in item['image_urls']:
                file_name = re.findall("[0-9a-z]*.jpg", image_url)
                if len(file_name) != 0:
                    file_name = file_name[0]
                file_path = '%s/%s' % (dir_path, file_name)

                if os.path.exists(file_path):
                    continue

                image_url = "http:" + image_url
                try:
                    urllib.urlretrieve(image_url, file_path)
                except socket.timeout:
                    print('urlretrieve timeout when fetching %s' % image_url)
                except Exception:
                    print('unknown exception')

                print("download %s to %s" % (image_url, file_path))

                ImageDownloadPipeline.stop_count += 1
            return item
```

使用urlretrieve时总是出现偶然超时的情况导致卡主，只好设置超时时间，捕捉异常，暂不知原因

10. 在settings.py中配置使用我们编写的pipeline
```python
# Configure item pipelines
# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
    'jiandan.pipelines.ImageDownloadPipeline': 300,
}

```

11. 执行scrapy crawl meizi

总结，整个流程有框架完成，我们只负责其中某个节点的具体实现
* scrapy作为框架依据start_url下载网页
* spider进行网页的解析，讲解析后的数据封装为特定item
* item被框架传递到pipeline，完成本地化存储


**截止到此，我们可以将jiandan网上的图抓取到本地了**，而实际上在这个简单的项目中是不需要用到pipeline的，直接在spider中将图片下载保存也做了就行。



### 解析器

爬虫最关键的地方在于如何解析爬下来的网页内容，python包含有很多库可以完成这项工作
* BeautifulSoup
* lxml   

Scrapy并未使用上述两种方式（当然我们也可以用），而是使用自己实现的解析器——Selector，Selector是基于lxml实现，它能够以两种方式解析HTML文档：XPath or CSS
* Xpath：基于路径的解析器，如
```python

# //title表示节点路径包含title，如/html/head/title, /html/head/body/div/title等
# text()表示获取文本内容
>>> body = '<html><head><title>good</title></head></html>'
>>> Selector(text=body).xpath('//span/text()').extract()
[u'good']

```
* CSS：基于style的解析器，同样解析title，如
```python
>>> response.xpath('//title/text()')
[<Selector (text) xpath=//title/text()>]
>>> response.css('title::text')
[<Selector (text) xpath=//title/text()>]

```

文档参考：<https://doc.scrapy.org/en/latest/topics/selectors.html>

### 如何编写spider
在spider的编写中，关键是如何解析response数据，可以在scrapy shell的环境中，先使用交互式方式测试解析代码：https://doc.scrapy.org/en/latest/intro/tutorial.html

1. 进入交互式环境

scrapy shell http://jandan.net/ooxx

2. 可以看到我们能够执行的操作有：
```python
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x7f90e65f9dd0>
[s]   item       {}
[s]   request    <GET http://jandan.net/ooxx>
[s]   response   <200 http://jandan.net/ooxx>
[s]   settings   <scrapy.settings.Settings object at 0x7f90e65f9e50>
[s]   spider     <DefaultSpider 'default' at 0x7f90e5c82b90>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects 
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser

In [9]: response.xpath('//title/text()').extract()
Out[9]: [u'\r\n\u59b9\u5b50\u56fe - \u714e\u86cb\u6b63\u7248\u8ba4\u8bc1\r\n']

#查看中文形式
In [14]: print response.xpath('//title/text()').extract()[0]
妹子图 - 煎蛋正版认证

#获取所有图片
In [15]: img = response.css('img').xpath('@src').extract()
In [16]: img
Out[16]: [u'//ws1.sinaimg.cn/mw600/dffab931gy1fhchn80sktj20o00w01f6.jpg',
 u'//wx4.sinaimg.cn/thumb180/005Hu1Gqly1fhcbdkp5ywg309l0an7wr.gif',
 u'//wx4.sinaimg.cn/thumb180/005Hu1Gqly1fhcbd5ewxpg30do0624qt.gif',
 u'//wx4.sinaimg.cn/thumb180/005Hu1Gqgy1fhcbeh3tjyg306y084kjl.gif',
...
 u'//ws1.sinaimg.cn/mw600/973dd02bgy1fhbg3m5fitj20nr0zkdmo.jpg']

#获取下一页链接
In [23]: response.xpath('//a[contains(@title, "Older Comments")]/@href').extract()
Out[23]: 
[u'http://jandan.net/ooxx/page-168#comments',
 u'http://jandan.net/ooxx/page-168#comments']

#获取一个链接即可
In [24]: response.xpath('//a[contains(@title, "Older Comments")]/@href').extract_first()
Out[24]: u'http://jandan.net/ooxx/page-168#comments'
 
```

如何使用response.xpath response.css，遇到具体问题参见：


### 如何设置停止条件
当spider抓取的速度往往比pipeline快，在抓取图片的例子中，我们在
* spider中获取图片的url
* 在pipeline中根据url将图片下载到本地

当我们只想要100张图，如果在spider中判断获取到第100个url时停止，那么此时pipeline中可能仅仅下载完10张，那么此时推出时不合适的。怎样设置停止合适的停止条件呢？

后续再说


### 如何处理js信息


### 如何配置多线程

