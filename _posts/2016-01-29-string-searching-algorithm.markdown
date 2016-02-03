---
title: '字符串匹配算法总结 Python实现'
guid: afdead47c03ce78d5674ca5b617b0383
comments: true
layout: post
tags:
     - python
     - algorithm
     - 算法
---
### Knuth–Morris–Pratt (KMP算法)
算法原理[参考](http://www.matrix67.com/blog/archives/115)  
算法实现[参考](https://leetcode.com/discuss/71495/java-and-python-solution-using-kmp-with-o-m-n-time-complexity)

``` python
def kmp(haystack, needle):
    i, j, m, n = -1, 0, len(haystack), len(needle)
    kmpnext = [-1] * n
    while j < n - 1:
        if i == -1 or needle[i] == needle[j]:
            i, j = i + 1, j + 1
            kmpnext[j] = i
        else:
            i = kmpnext[i]
    i = j = 0
    while i < m and j < n:
        if j == -1 or haystack[i] == needle[j]:
            i, j = i + 1, j + 1
        else:
            j = kmpnext[j]
    if j == n:
        return i - j
    return -1
```

### Boyer-Moore-Horspool (Horspool)
算法原理[参考](http://www.ruanyifeng.com/blog/2013/05/boyer-moore_string_search_algorithm.html)  
算法实现[参考](http://www.cnblogs.com/cobbliu/archive/2012/05/29/2524244.html)

``` python
def horspool(self, haystack, needle):
    m, n = len(haystack), len(needle)
    if m < n:
        return -1
    # Use defaultdict as bad char shift table instead of list
    bad_char_jump = defaultdict(lambda: n)
    for i in xrange(n - 1):
        bad_char_jump[needle[i]] = n - i - 1
    pos = 0
    while pos <= m - n:
        j = n - 1
        while j >= 0 and needle[j] == haystack[pos + j]:
            j -= 1
        if j == -1:
            return pos
        else:
            pos += bad_char_jump[haystack[pos + n - 1]]
    return -1
```

### Sunday

算法[参考](http://kmplayer.iteye.com/blog/704187)

``` python
def sunday(self, haystack, needle):
    m, n = len(haystack), len(needle)
    if m < n:
        return -1
    bad_char_jump = defaultdict(lambda: n + 1)
    for i in range(n):
        bad_char_jump[needle[i]] = n - i
    pos = 0
    while pos <= m - n:
        i, j = pos, 0
        while j < n and haystack[i] == needle[j]:
            i += 1
            j += 1
        if j == n:
            return pos
        elif pos == m - n:
            return -1
        else:
            pos += bad_char_jump[haystack[pos + n]]
    return -1
```

### 算法验证
全部代码通过 [Leetcode 28. Implement strStr()](https://leetcode.com/problems/implement-strstr/) 测试。