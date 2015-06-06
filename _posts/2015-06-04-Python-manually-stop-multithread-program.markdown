---
title: 'Python手动中断(Ctrl-C)多线程程序'
guid: aaee9e268344a45144a6c01f2032a043
comments: true
layout: post
tags:
     - python
     - 多线程
---
### 引
灵感来源依旧是爬虫框架项目[pycrawler](https://github.com/pengmeng/PyCrawler)，爬虫作为子线程运行时不受键盘中断信号影响，Ctrl-C无法终止整个爬虫运行。另外的一个场景是多线程压力测试，需要提前终止的情况下，Ctrl-C依旧不能终止整个程序。除了简单粗暴的使用kill命令强行终止之外，本文将给出一个简单可行的解决方案。  
值得注意的一点是，Python2、3两个版本在测试中的表现并不一致，所以使用两个版本分别进行测试。

### 测试环境

 - Python2 2.7.9
 - Python3 3.4.2
 - Mac OS X Yosemite 10.10.3

### 子线程类
``` python
import time
from threading import Thread


class CountDown(Thread):
    def __init__(self):
        super(CountDown, self).__init__()

    def run(self):
        num = 100
        print('slave start')
        for i in range(10, 0, -1):
            print('Num: {0}'.format(num/i))
            time.sleep(1)
        print('slave end')
```

### 失败情况一
主线程代码

``` python
if __name__ == '__main__':
    print('main start')
    CountDown().start()
    print('main end')
```

使用Python2进行测试，在运行结束之前手动终止：

```
main start
slave start
 main end
Num: 10
Num: 11
^CNum: 12
Num: 14
Num: 16
Num: 20
Num: 25
Num: 33
Num: 50
Num: 100
slave end
Exception KeyboardInterrupt in <module 'threading' from '/usr/local/Cellar/python/2.7.9/Frameworks/Python.framework/Versions/2.7/lib/python2.7/threading.pyc'> ignored
```

可以看到，运行到第三行时手动终止，主线程已经提前结束，子线程继续执行直到结束，然后抛出未捕获异常，值得注意的是，此时没有```Traceback```输出。  
接下来使用Python3测试同样的代码：

```
main start
slave start
main end
Num: 10.0
Num: 11.11111111111111
Num: 12.5
^CException ignored in: <module 'threading' from '/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/threading.py'>
Traceback (most recent call last):
  File "/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/threading.py", line 1294, in _shutdown
    t.join()
  File "/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/threading.py", line 1060, in join
    self._wait_for_tstate_lock()
  File "/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/threading.py", line 1076, in _wait_for_tstate_lock
    elif lock.acquire(block, timeout):
KeyboardInterrupt
```
有趣的事情发生了，主线程依旧提前退出，子线程在手动终止后也被强行终止，虽然打印了```Traceback```信息，但和上例一样依旧是```Exception ignored```。  

### 失败情况二
主线程代码，现在调用join()方法使主线程等待子线程完成：

``` python
if __name__ == '__main__':
    print('main start')
    td = CountDown()
    td.start()
    td.join()
    print('main end')

```

同上，使用Python2进行测试：

```
main start
slave start
Num: 10
Num: 11
Num: 12
^CNum: 14
Num: 16
Num: 20
Num: 25
Num: 33
Num: 50
Num: 100
slave end
Traceback (most recent call last):
  File "multithread.py", line 23, in <module>
    td.join()
  File "/usr/local/Cellar/python/2.7.9/Frameworks/Python.framework/Versions/2.7/lib/python2.7/threading.py", line 949, in join
    self.__block.wait()
  File "/usr/local/Cellar/python/2.7.9/Frameworks/Python.framework/Versions/2.7/lib/python2.7/threading.py", line 340, in wait
    waiter.acquire()
KeyboardInterrupt
```

可以看出，主线程调用join()方法之后wait在一个锁上，等待子线程退出。子线程退出后，主线程获得锁并响应中断信号，抛出异常并打印信息，```main end```一行并没有被打印。  
然后使用Python3进行测试：

```
main start
slave start
Num: 10.0
Num: 11.11111111111111
Num: 12.5
^CTraceback (most recent call last):
  File "multithread.py", line 23, in <module>
    td.join()
  File "/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/threading.py", line 1060, in join
    self._wait_for_tstate_lock()
  File "/usr/local/Cellar/python3/3.4.3/Frameworks/Python.framework/Versions/3.4/lib/python3.4/threading.py", line 1076, in _wait_for_tstate_lock
    elif lock.acquire(block, timeout):
KeyboardInterrupt
Num: 14.285714285714286
Num: 16.666666666666668
Num: 20.0
Num: 25.0
Num: 33.333333333333336
Num: 50.0
Num: 100.0
slave end
```

神奇的事情再次发生了，主线程等待子线程过程中响应了中断信号，打印信息后退出，而此时子线程没有受影响，继续执行直到结束。

### 思考
从Python3的```Traceback```信息可以看出，即使没有显式调用join()方法，主线程执行完成后依然会自动调用一个join()逻辑（```line 1294, in _shutdown```），而且个方法对子线程的影响并不一致：显式调用时子线程不受影响继续执行；而自动调用时，子线程随主线程一起退出。  
根据```Traceback```信息查看Python3源代码：

``` python
def _shutdown():
    # Obscure:  other threads may be waiting to join _main_thread.  That's
    # dubious, but some code does it.  We can't wait for C code to release
    # the main thread's tstate_lock - that won't happen until the interpreter
    # is nearly dead.  So we release it here.  Note that just calling _stop()
    # isn't enough:  other threads may already be waiting on _tstate_lock.
    tlock = _main_thread._tstate_lock
    # The main thread isn't finished yet, so its thread state lock can't have
    # been released.
    assert tlock is not None
    assert tlock.locked()
    tlock.release()
    _main_thread._stop()
    t = _pickSomeNonDaemonThread()
    while t:
        t.join()
        t = _pickSomeNonDaemonThread()
    _main_thread._delete()

def _pickSomeNonDaemonThread():
    for t in enumerate():
        if not t.daemon and t.is_alive():
            return t
    return None
```

从源代码可以看出，主线程调用了```_stop()```方法，然后循环等待所有非daemon进程执行结束，最终调用```_delete()```方法结束运行。所以主线程虽然执行完了所有的代码，但是其实并未真正退出，而是等待所有非daemon子线程全部执行完毕后才释放资源退出程序（所有daemon线程也随之被销毁），这个过程中，主线程仅仅占有资源但并没有执行逻辑（这里我的理解是，不会响应中断信号）。  
所以，得出结论：

 - 没有调用```join()```的情况下，主线程退出执行逻辑但没有释放资源，且不响应中断信号，此时中断信号由子线程响应，于是在失败情况一中，程序成功被终止。
 - 显式调用```join()```的情况下，主线程没有执行后续代码，而是等待子线程释放锁，因此可以响应中断信号，于是在失败情况二中，主线程响应中断信号并执行退出逻辑（进入上一种情况），子线程并未受影响，执行完毕后程序退出。

***但是对于Python2和Python3之间的差别，我现在依旧没有想明白，初步的想法是2和3对于异常的处理逻辑（或者说顺序）不一致，导致2中所有的异常都在主线程真正退出时才被捕获。打算去知乎问一下，之后会补上问题链接***

### 解决方案
说了这么多终于到解决方案了。思路是：设置子线程为daemon线程，启动子线程后主线程调用```isAlive()```方法手动模拟join过程。  
代码如下：

``` python
if __name__ == '__main__':
    print('main start')
    td = CountDown()
    td.setDaemon(True)
    td.start()
    try:
        while td.isAlive():
            pass
    except KeyboardInterrupt:
        print('stopped by keyboard')
    print('main end')
```

测试输出（Python2、3执行结果一致）：

```
main start
slave start
Num: 10
Num: 11
Num: 12
Num: 14
Num: 16
^Cstopped by keyboard
main end
```

此方案另一优点是主线程可以继续执行之后的善后逻辑。

### 结
 - 感觉这种解决方案算是非主流小技巧了，我想了好久才想出来，具体应用中实不实用还不知道，毕竟现在接触的项目都太小了。
 - 从这个例子看出，Python2和Python3之间除了官网提到的关键区别，还有很多细微的差别，这些差别对某些特定程序可能会产生一定影响，只能慢慢摸索着发现了。
 - 这篇文章算是多线程tricky技巧第一篇。多线程程序中另一个重要问题是主线程如何捕获子线程产生的异常，我构思了一个方案，有时间测试一下。
 - 如果有哪些不对的地方或者有更好的解决方案，欢迎讨论。
 - 谢谢~
