---
title: 'Python 并发网络库 eventlet 性能测试'
guid: 442001dadf0790ce3cb09adb4f7aa106 
comments: true
layout: post
tags:
     - python
---

### 为什么写这篇测试
之前写了一个Python的轻量级爬虫框架[pycrawler](https://github.com/pengmeng/PyCrawler)，因为爬虫属于IO密集型程序，因此想到了使用并发，但Python本身对于并发的支持并不好，于是改为使用并发网络库[eventlet](http://eventlet.net).
eventlet库用法非常简单，可以指定并发访问的url数，框架的最初版本把这个数值设为10。但是之后的项目中需要爬取140k+ url，10个url并发访问的话效率非常低。查了eventlet的文档之后并没有发现关于最大并发数量的限制，只有这么一句话：
> Note also that imap is memory-bounded and won’t consume gigabytes of memory if the list of urls grows to the tens of thousands (yes, we had that problem in production once!).

所以我尝试将并发数设置为1000，这个尝试的确的确提高了效率，但是抓取的结果却出现了问题。项目中url从网页中解析出来添加到任务队列，当并发数为1000时，爬行完成后总url访问数只有80k，远小于预期的140k。说明爬行过程中出现了不可预期的问题。
于是想探究一下eventlet库最佳的并发数量。

### 测试环境
 - Python 2.7
 - Mac OS X Yosemite 10.10.3
 - eventlet 0.17.3

### 测试思路
基本的测试思路是并发下载同一个页面，计算总耗时和平均耗时，并且统计总返回页面数是否和访问url数相等。
测试代码如下：
``` python
import time
import eventlet
from eventlet.green import urllib2


def fetchone(url):
    try:
        res = urllib2.urlopen(url)
    except (IOError, urllib2.HTTPError) as e:
        html = None
    else:
        html = res.read()
    return html


def fetch(urls):
    pool = eventlet.GreenPool()
    start = time.time()
    results = pool.imap(fetchone, urls)
    end = time.time()
    count = 0
    for html in results:
        if html:
            count += 1
    ms = (end - start) * 1000
    total = round(ms, 4)
    ave = round(ms / len(urls), 4)
    print('Try {0} urls and get {1} responses. Total {2}ms average {3}ms'.format(len(urls), count, total, ave))

if __name__ == '__main__':
    base = ['http://www.zhihu.com']
    fetch(base)
    for i in xrange(10, 110, 10):
        fetch(base*i)
```
最初使用Google主页进行测试，但由于访问太频繁被Google 503了。而且因为我在美国测试，Google的响应时间太短，于是改用知乎主页进行测试。
测试结果如下：
```
Try 1 urls and get 1 responses. Total 0.9949ms average 0.9949ms
Try 10 urls and get 10 responses. Total 0.041ms average 0.0041ms
Try 20 urls and get 20 responses. Total 0.041ms average 0.0021ms
Try 30 urls and get 30 responses. Total 0.0401ms average 0.0013ms                                                          
Try 40 urls and get 40 responses. Total 0.042ms average 0.001ms
Try 50 urls and get 50 responses. Total 0.0479ms average 0.001ms
Try 60 urls and get 60 responses. Total 0.061ms average 0.001ms
Try 70 urls and get 70 responses. Total 0.0451ms average 0.0006ms
Try 80 urls and get 80 responses. Total 0.0429ms average 0.0005ms
Try 90 urls and get 90 responses. Total 0.041ms average 0.0005ms
Try 100 urls and get 100 responses. Total 0.0448ms average 0.0004ms
```

### 结果分析
从测试结果可以看出，单线程访问耗时最长，这应该是eventlet内部实现导致的。随着并发数的提高，平均耗时逐渐变短。在本测试范围中，并发数为100时，平均耗时为0.0004ms。因此猜测继续提高并发数后，平均耗时会继续变短直到达到某一个最小值，然后增加。我尝试提高并发数至1000，但结果是产生了异常。
考虑到对目标服务器的压力以及继续提高并发数所能够带来的性能提升，因此并发数100可以成为相对的最优解。实际项目中可以设计自适应算法来动态改变并发数来实现性能的最优化。

### 声明
 - 第一次在简书上写技术文章，应该会有诸多纰漏，希望各位前辈可以不吝赐教。
 - 测试代码有很多不严谨的地方，例如没有考虑到不同时间的网络状况的差别。
 - 文章开头提到的爬虫框架是闲来无事写的第一个开源项目，不完善的地方请大家指正。
