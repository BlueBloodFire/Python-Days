# Days-07

## 基于HTTP协议的网络资源访问

### HTTP(超文本传输协议)

超文本传输协议是一种用于分布式、协作式和超媒体信息系统的应用层协议，它是万维网数据通信的基础，设计HTTP最初的目的是为了提供一种发布和接收HTML页面的方法，通过HTTP或者HTTPS请求的资源由URL来标识。简单地说，通过HTTP我们可以获取网络上地资源，开发中经常会用到地网络API就是基于HTTP来实现数据传输的。

### JSON格式

JSON是一种轻量级的数据交换语言，该语言以易于让人阅读的文字为基础，用来传输由属性值或序列性的值组成的数据对象。相较于XML，JSON显得更加的轻便和优雅，下面可以比较下

```
#XML
<?xml version="1.0" encoding="UTF-8"?>
<message>
        <from>Alice</from>
        <to>Bob</to>
        <content>Will you marry me?</content>
</message>
```
```
#JSON
{
    "from":"Alice",
    "to":"Bob",
    "content":"Will you marry me?"
}
```

### requests库

一个基于HTTP协议来使用网络的第三库，可以避免安全缺陷、冗余代码。
```
from time import time
from threading import Thread

import requests


# 继承Thread类创建自定义的线程类
class DownloadHanlder(Thread):

    def __init__(self, url):
        super().__init__()
        self.url = url

    def run(self):
        filename = self.url[self.url.rfind('/') + 1:]
        resp = requests.get(self.url)
        with open('/Users/Hao/' + filename, 'wb') as f:
            f.write(resp.content)


def main():
    # 通过requests模块的get函数获取网络资源
    # 下面的代码中使用了天行数据接口提供的网络API
    # 要使用该数据接口需要在天行数据的网站上注册
    # 然后用自己的Key替换掉下面代码的中APIKey即可
    resp = requests.get(
        'http://api.tianapi.com/meinv/?key=APIKey&num=10')
    # 将服务器返回的JSON格式的数据解析为字典
    data_model = resp.json()
    for mm_dict in data_model['newslist']:
        url = mm_dict['picUrl']
        # 通过多线程的方式实现图片下载
        DownloadHanlder(url).start()


if __name__ == '__main__':
    main()
```

