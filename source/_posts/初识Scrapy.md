---
title: 初识Scrapy
date: 2019-10-08 15:25:50
tags:
---

#### 什么是Scrapy？

`Scrapy`使用 `Python` 实现的一个开源爬虫框架，`Scrapy`基于 `twisted`这个高性能的事件驱动网络引擎框架，`Scrapy`爬虫拥有很高的性能。

1. `Scrapy`内置数据提取器（`Selector`），支持`XPath`和 `Scrapy`自己的 `CSS Selector`语法
2. 并且支持正则表达式，方便从网页提取信息。
3. 交互式的命令行工具，方便测试 `Selector` 和 `debugging`爬虫
4. 支持将数据导出为 `JSON，CSV，XML`格式。
5. 可推展性强，运行自己编写特定功能的插件
6. 内置了很多拓展和中间件用于处理：
	* `cookies`和 `session`
	* `HTTP`的压缩，认证，缓存
	* `robots.txt`
	* 爬虫深度限制
	
<!--more-->


#### Scrapy流程图

{% asset_img GkjK7BGIB0.png This is an example image %}

其中：

* `Scrapy Engine(引擎)`: 负责`Spider`、`ItemPipeline`、`Downloader`、`Scheduler`中间的通讯，信号、数据传递等。
* `Scheduler(调度器)`: 它负责接受引擎发送过来的Request请求，并按照一定的方式进行整理排列，入队，当`引擎`需要时，交还给`引擎`。
* `Downloader（下载器）`：负责下载`Scrapy Engine(引擎)`发送的所有Requests请求，并将其获取到的Responses交还给`Scrapy Engine(引擎)`，由`引擎`交给`Spider`来处理，
* `Spider（爬虫）`：它负责处理所有Responses,从中分析提取数据，获取Item字段需要的数据，并将需要跟进的URL提交给`引擎`，再次进入`Scheduler(调度器)`，
* `Item Pipeline(管道)`：它负责处理`Spider`中获取到的Item，并进行进行后期处理（详细分析、过滤、存储等）的地方.
* `Downloader Middlewares（下载中间件）`：你可以当作是一个可以自定义扩展下载功能的组件。
* `Spider Middlewares（Spider中间件）`：你可以理解为是一个可以自定扩展和操作`引擎`和`Spider`中间`通信`的功能组件（比如进入`Spider`的Responses;和从`Spider`出去的Requests）

#### 制作 Scrapy 爬虫 的步骤?

``` bash
新建项目(scrapy startproject xxx)：新建一个新的爬虫项目
明确目标（编写items.py）：明确你想要抓取的目标
制作爬虫（spiders/xxspider.py）：制作爬虫开始爬取网页
存储内容（pipelines.py）：设计管道存储爬取内容
```

#### 如何安装Scrapy?

#### 在windows系统下安装Scrapy?

在windows 64bit系统下安装Scrapy需要安装以下依赖库：

``` bash
pip install wheel
lxml-4.2.1-cp36-cp36m-win_amd64.whl
pyOpenSSL-17.5.0-py2.py3-none-any.whl
pywin32-221.win-amd64-py3.6.exe
Twisted-17.9.0-cp36-cp36m-win_amd64.whl
pip install scrapy
```

#### 在linux下安装Scrapy

系统版本为ubuntu 16.04

```
sudo apt-get install build-essential python3-dev libssl-dev libffi-dev libxml2 libxml2-dev libxslt1-dev zlib1g-dev
pip install scrapy
```

#### Scrapy文件结构

我们在windows命令行模式下输入以下命令创建Scrapy项目：

```
scrapy startproject 项目名称
```

可以看到创建了以下文件：

{% asset_img C8bHEFJIJ3.png This is an example image %}

其中：

```
scrapy.cfg ：项目的配置文件
xxSpider/ ：项目的Python模块，将会从这里引用代码
xxSpider/items.py ：项目的目标文件
xxSpider/pipelines.py ：项目的管道文件
xxSpider/settings.py ：项目的设置文件
xxSpider/spiders/ ：存储爬虫代码目录
```

#### Scrapy单文件demo

创建完Scrapy项目，还是要上手实验一下才能更好的理解，所以我根据之前我在楼+课程中的学习笔记写了一个Scrapy单文件Demo，使用这个单文件Demo能快速爬取实验楼全部课程信息。
首先看下单文件的内容结构：

```Python
# -*- coding:utf-8 -*-
import scrapy
class ShiyanlouCoursesSpider(scrapy.Spider):
    """ 所有 scrapy 爬虫需要写一个 Spider 类，这个类要继承 scrapy.Spider 类。在这个类中定义要请求的网站和链接、如何从返回的网页提取数据等等。
    """
    # 爬虫标识符号，在 scrapy 项目中可能会有多个爬虫，name 用于标识每个爬虫，不能相同
    name = 'shiyanlou-courses'
    def start_requests(self):
        """ 需要返回一个可迭代的对象，迭代的元素是scrapy.Request对象，可迭代对象可以是一个列表或者迭代器，这样 scrapy 就知道有哪些网页需要爬取了。scrapy.Request接受一个 url 参数和一个 callback 参数，url 指明要爬取的网页，callback 是一个回调函数用于处理返回的网页，通常是一个提取数据的 parse 函数。
        """
    def parse(self, response):
        """ 这个方法作为 `scrapy.Request` 的 callback，在里面编写提取数据的代码。scrapy 中的下载器会下载 `start_reqeusts` 中定义的每个 `Request` 并且结果封装为一个 response 对象传入这个方法。
        """
        pass
```

因为实验楼的网页结构还是很简单的，所以解析部分就不做赘述，直接上单文件完整代码：

```Python
# -*- coding:utf-8 -*-
import scrapy
class ShiyanlouCoursesSpider(scrapy.Spider):
    def start_requests(self):
    # 课程列表页面 url 模版
    url_tmpl = 'https://www.shiyanlou.com/courses/?category=all&course_type=all&fee=all&tag=all&page={}'
    # 所有要爬取的页面
    urls = (url_tmpl.format(i) for i in range(1, 23))
    # 返回一个生成器，生成 Request 对象，生成器是可迭代对象
    for url in urls:
        yield scrapy.Request(url=url, callback=self.parse)
    def parse(self, response):
    # 遍历每个课程的 div.course-body
    for course in response.css('div.course-body'):
        # 使用 css 语法对每个 course 提取数据
        yield {
            # 课程名称
            'name': course.css('div.course-name::text').extract_first(),
            # 课程描述
            'description': course.css('div.course-desc::text').extract_first(),
            # 课程类型，实验楼的课程有免费，会员，训练营三种，免费课程并没有字样显示，也就是说没有 span.pull-right 这个标签，没有这个标签就代表时免费课程，使用默认值 `免费｀就可以了。
            'type': course.css('div.course-footer span.pull-right::text').extract_first(default='Free'),
            # 注意 // 前面的 .，没有点表示整个文档所有的 div.course-body，有 . 才表示当前迭代的这个 div.course-body
               'students': course.xpath('.//span[contains(@class, "pull-left")]/text()[2]').re_first('[^\d]*(\d*)[^\d]*')
        }
```

保存文件，使用scrapy runspider xx.py -o data.json运行代码，这里使用 -o参数将结果输出为json格式。
