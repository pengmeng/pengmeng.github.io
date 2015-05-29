---
title: '从代码计时想到的Python计时模块、上下文管理器、变量生存期'
guid: 294c2829e2803b25f132b31600b59ff2
comments: true
layout: post
tags:
    - python
---
### Setup
[前一篇](http://www.jianshu.com/p/1798052d086e)文章中使用了代码计时的功能，使用time模块获得起始和终止时间然后相减获得运行时间。

``` python
import time

start = time.time()
# code to run
end = time.time()
cost = end - start #time in second
```

Python中除了这种最原始的计时方案，还提供了专门的计时模块```timeit```，该模块提供了较为完善的计时功能，而且支持重复测试，更多的教程中提倡使用这种方案。以下例子来自[博客](http://www.cnblogs.com/moinmoin/archive/2011/03/18/python-runtime-measuring.html)

``` python
def test1():
    n=0
    for i in range(101):
        n+=i
    return n

def test2():
    return sum(range(101))

def test3():
    return sum(x for x in range(101))

if __name__=='__main__':
    from timeit import Timer
    t1=Timer("test1()","from __main__ import test1")
    t2=Timer("test2()","from __main__ import test2")
    t3=Timer("test3()","from __main__ import test3")
    print t1.timeit(1000000)
    print t2.timeit(1000000)
    print t3.timeit(1000000)
    print t1.repeat(3,1000000)
    print t2.repeat(3,1000000)
    print t3.repeat(3,1000000)
```

以上是两种较为常见的计时功能，有相应的优缺点：

 - 第一种方案较为直观，需要了解```time```模块的使用方法，并且需要进行单位转换
 - 第二种方案有官方支持，但是不方便用于测量非函数代码块（例如某几行代码的运行时间），而且需要通过参数来配置测试环境  

基于以上考虑，我想能不能实现一种更加灵活的代码计时工具。

### 上下文管理器 (Context Manager)
初见这个概念可能有点陌生，但下面的代码大家一定再熟悉不过了：

``` python
with open('afile.txt', 'r') as f:
    content = f.read()
```

上下文管理器是Python 2.5之后引入的概念，也就是常用的```with```语句。上下文管理器大大减轻了操作一些需要打开和关闭的资源（这里找不到好的定语了，就是文件和数据库这种资源）的代码。更重要的一点是，```with```语句使用很灵活，内部可以包含任意的代码。
实现上下文管理器有两种方法，分别是在自定义类中实现```__enter()__``` ```__exit()__```和使用装饰器```@contextlib.contextmanager```

### 变量生存期
Python对于变量生存期的处理很神奇，这里不讨论太多的内容，只指出两点较为特别的地方：

 - 上例中```content```是在```with```中定义的变量，但是在外部依旧可以使用
 - Python中的循环变量在循环结束之后依旧可以访问到:

``` python
for i in range(10):
    pass
print(i) # 9
```

### timeme
基于以上讨论，实现一个用于代码计时的上下文管理器，代码已托管到[gist](https://gist.github.com/pengmeng/78a25663c20ab8890b81)，之后只在gist更新，文中代码不再更新。
这个模块仅仅用于学习上下文管理器和代码计时，不恰当的地方欢迎大家指正。

```  python
__author__ = 'mengpeng'
import time
 
 
class timeme(object):
    __unitfactor = {'s': 1,
                    'ms': 1000,
                    'us': 1000000}
 
    def __init__(self, unit='s', precision=4):
        self.start = None
        self.end = None
        self.total = 0
        self.unit = unit
        self.precision = precision
 
    def __enter__(self):
        if self.unit not in timeme.__unitfactor:
            raise KeyError('Unsupported time unit.')
        self.start = time.time()
        return self
 
    def __exit__(self, exc_type, exc_val, exc_tb):
        self.end = time.time()
        self.total = (self.end - self.start) * timeme.__unitfactor[self.unit]
        self.total = round(self.total, self.precision)
 
    def __str__(self):
        return 'Running time is {0}{1}'.format(self.total, self.unit)
```

实现了基本的时间单位设置、精确位数设置，并提供了默认的输出字符串。

``` python
from timeme import timeme
with timeme('ms', 6) as t:
    result = sum(range(100000))
print(result) # 4999950000
print(t.total) # 5.503178
print(t) # Running time is 5.503178ms
```

在一些简单的计时任务中，相比于使用```timeit```模块更加灵活而且无需手动配置运行环境；相比于使用手动计时减少了代码行数。

### Teardown
 - 前几天闲得无聊写了这个模块，很多都是得益于Python本身机制的便利。
 - 还有很多值得改善的地方，例如重复测试```timeit.repeat()```的功能支持。
 - 在代码代码计时、运行效率瓶颈检测方面，Python有很多完善的Profiler工具提供了相当强大的功能，我对这方面还不是很熟悉，最近打算学习一下。
