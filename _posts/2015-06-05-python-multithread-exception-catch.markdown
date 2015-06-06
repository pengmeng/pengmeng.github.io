---
title: 'Python 主线程捕获子线程异常'
guid: 2ffeffd04fc10fda2b5213c828b27265
comments: true
layout: post
tags:
     - python
     - 多线程
     - 异常
---

### 多线程异常
接上一篇[Python手动中断(Ctrl-C)多线程程序](http://pengmeng.me/2015/06/04/Python-manually-stop-multithread-program.html)，这一篇来探讨多线程程序中另一个问题：主线程捕获子线程异常。

### 为什么会产生这个问题
先来看这个问题对应的代码是怎样的：

``` python
import time
from threading import Thread


class CountDown(Thread):
    def __init__(self):
        super(CountDown, self).__init__()

    def run(self):
        num = 100
        print('slave start')
        for i in range(5, -1, -1):
            print('Num: {0}'.format(num/i))
            time.sleep(1)
        print('slave end')


if __name__ == '__main__':
    print('main start')
    try:
        td = CountDown()
        td.start()
        td.join()
    except Exception as e:
        print(e)
    print('main end')
```

代码很直观，每秒打印一个值，当```i==0```时，除法会抛出```ZeroDivisionError: integer division or modulo by zero```异常。代码在2、3中表现基本一致，因此只讨论Python2的情况。  
执行上述代码，得到下面的输出：

```
main start
slave start
Num: 20
Num: 25
Num: 33
Num: 50
Num: 100
Exception in thread Thread-1:
Traceback (most recent call last):
  File "/System/Library/Frameworks/Python.framework/Versions/2.7/lib/python2.7/threading.py", line 810, in __bootstrap_inner
    self.run()
  File "multithread.py", line 14, in run
    print('Num: {0}'.format(num/i))
ZeroDivisionError: integer division or modulo by zero

main end
```

可见，子线程产生了异常但并没有被主线程所捕获，因为主线程和子线程分别使用各自的栈，主线程并不能截获子线程调用过程中的异常。在子线程异常退出后，主线程执行了后续代码（此时主线程不知道子线程的退出状态）。

### 解决方案
将代码修改为：

``` python
import time
import sys
import traceback
from threading import Thread


class CountDown(Thread):
    def __init__(self):
        super(CountDown, self).__init__()
        self.exitcode = 0
        self.exception = None
        self.exc_traceback = ''

    def run(self):
        try:
            self._run()
        except Exception as e:
            self.exitcode = 1
            self.exception = e
            self.exc_traceback = ''.join(traceback.format_exception(*sys.exc_info()))

    def _run(self):
        num = 100
        print('slave start')
        for i in range(5, -1, -1):
            print('Num: {0}'.format(num/i))
            time.sleep(1)
        print('slave end')


if __name__ == '__main__':
    print('main start')
    td = CountDown()
    td.start()
    td.join()
    if td.exitcode != 0:
        print('Exception in ' + td.getName() + ' (catch by main)')
        print(td.exc_traceback)
    print('main end')
```

由于子线程运行结束后，其内存并没有被回收，因此可以继续使用该实例获得其成员变量。基于这一点，我们可以通过添加模拟线程退出信息的成员变量来记录子线程退出状态。可通过```sys```和```traceback```两个库实现这一点（[完整Demo](https://docs.python.org/2/library/traceback.html)）。通过检查子线程```exitcode```，主线程可以知道其退出状态，然后做相应的处理。  
代码输出：

```
main start
slave start
Num: 20
Num: 25
Num: 33
Num: 50
Num: 100
Exception in Thread-1 (catch by main)
Traceback (most recent call last):
  File "multithread.py", line 17, in run
    self._run()
  File "multithread.py", line 27, in _run
    print('Num: {0}'.format(num/i))
ZeroDivisionError: integer division or modulo by zero

main end
```

可以看出，该方法可以在主线程中获得子线程的异常信息，从而为debug提供了依据。除此之外，因为子线程抛出的异常大多为```RuntimeError```（其他异常应该在子线程内部处理），主线程检测到子线程异常退出后可以做一些相应的处理及恢复。