

```python
In [28]: url="http://cas.tp-link.net:8080/cas/login?return=http://tp-link.net/"

In [29]: params = {'username':'xxxx@xxoo.com', 'password':'xxxx'}

In [30]: r = requests.post(url, data=params)

In [31]: r.text

```

但上诉过程无法管理cookie信息，使用cookielib获取cookie

```python
import urllib2
import cookielib
#声明一个CookieJar对象实例来保存cookie
cookie = cookielib.CookieJar()
#利用urllib2库的HTTPCookieProcessor对象来创建cookie处理器
handler=urllib2.HTTPCookieProcessor(cookie)
#通过handler来构建opener
opener = urllib2.build_opener(handler)
#此处的open方法同urllib2的urlopen方法，也可以传入request
response = opener.open('http://www.baidu.com')
for item in cookie:
    print 'Name = '+item.name
    print 'Value = '+item.value

```

模拟登录

```python
import urllib2
import urllib
import cookielib
cookie = cookielib.CookieJar()
handler=urllib2.HTTPCookieProcessor(cookie)
opener = urllib2.build_opener(handler)

headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36',
            'Host':'cas.tp-link.net:8080',
            'Referer':'http://tp-link.net/'
          }


postdata = urllib.urlencode({
			'username':'jiangtingyu@tp-link.com.cn',
			'password':'jtyW8223'
		})

url = 'http://cas.tp-link.net:8080/cas/login?return=http://tp-link.net/'

response = opener.open(url, data=postdata)
for item in cookie:
    print 'Name = '+item.name
    print 'Value = '+item.value

from bs4 import BeautifulSoup
## BeautifulSoup 中文文档：https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html
html = response.readlines()
soup=BeautifulSoup(html,'lxml')



In [19]: print(response.headers)
Date: Wed, 12 Jul 2017 11:52:50 GMT
Server: Apache
P3P: CP="NOI ADM DEV PSAi COM NAV OUR OTRo STP IND DEM"
Cache-Control: no-cache
Pragma: no-cache
Vary: Accept-Encoding
Connection: close
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8

```
