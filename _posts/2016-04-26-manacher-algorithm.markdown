---
title: "Manacher's Algorithm: 求最长回文子串"
guid: ef1006bb98302fc163cf4c839fd20581
comments: true
layout: post
tags:
     - algorithm
     - 算法
---

### 最长回文子串问题
求一个字符串的最长回文子串是一个经典的算法问题[Wikipedia](https://en.wikipedia.org/wiki/Longest_palindromic_substring)。这个问题有很多种经典解法，例如最简单的O(n**2)解法，遍历字符串以每一个字符为对称轴向两端扩展，分别试探奇数长度和偶数长度回文串，最后找到最长的一个。显然我们可以将时间复杂度进一步优化。

### Manacher's Algorithm
Manacher's Algorithm 是一个相当巧妙的算法，时间复杂度O(n)，这篇文章中争取把这个算法解释清楚，并附上Python实现。这个算法理解了之后还是很容易记忆的。算法解释[参考](http://articles.leetcode.com/longest-palindromic-substring-part-ii/)。  
为了求得最长回文子串，我们需要有一个数组来记录以每个字符为对称中心的回文长度：

``` python
s = a a c e c a a a
P = 0 0 0 3 0 0 1 0
```
如果有了数组P，我们可以容易的求出max(P)然后求得最长子串为`aacecaa`。但是这个方法是不对的，因为没有考虑回文子串长度为偶数的情况。Manacher's Algorithm中使用了很巧妙的一个预处理方法，将偶数长度情况转化为奇数长度处理，构造新的字符串t，`t = '#{}#'.format('#'.join(s))`，此时t和数组P转化为：

``` python
t = # a # a # c # e # c # a # a # a #
P = 0 1 2 1 0 1 0 7 0 1 0 1 2 3 2 1 0
```
这种情况下类似于`aa`这样的偶数子串就可以被考虑进去了。  
实际应用过程中，为了避免边界检查，在t串前后分别加入不相等且不出现在原始字符串中的两个字符：

``` python
t = ^ # a # a # c # e # c # a # a # a # $
```

那么现在问题就转变为如何求得数组P，从上面可以看出，数组P在一定范围内具有对称性，`aceca`和`aaa`都具有这种对称性。Manacher's Algorithm中就是利用这种对称性来简化求解P的过程。上面的例子不能很好的展示计算过程，这里换一个新的例子`s = 'babcbabcbaccba'`：

``` python
t = ^ # b # a # b # c # b # a # b # c # b # a # c # c # b # a # $
P = 0 0 1 0 3 0 1 0 7 0 1 0 9 0 1 0 5 0 1 0 1 0 1 2 1 0 1 0 1 0 0
```

那么如何使用这种对称性来计算P呢？我们考虑计算过程中的几种情况：

``` python
index	        3             10  12  14            21
val		        L                 C                 R
t 		= ^ # b # a # b # c # b # a # b # c # b # a # c # c # b # a # $
P 		= 0 0 1 0 3 0 1 0 7 0 1 0 9 0 ?
```
这种情况下C是子串`abcbabcba`的对称中心，L、R分别为左右边界，需要求得`index = 14`时对应P的值，利用对称性可以求出`index' = 10`，然后很明显可以发现`P[14] = P[10] = 1`。通过验证可以发现对于`index = 13 14 15`三种情况，P的值都可以通过对称性求得。

``` python
index	        3         8   10  12      16        21
val		        L                 C                 R
t 		= ^ # b # a # b # c # b # a # b # c # b # a # c # c # b # a # $
P 		= 0 0 1 0 3 0 1 0 7 0 1 0 9 0 1 0 ?
```
当`index = 16`时，根据上述对称性求出`P[16] = P[8] = 7`，但是通过验证发现，这个结果是不对的，实际上`P[16] = 5`。此时对于`index = 8`而言，其左边界在L左侧，此时如果使用对称性的话以`index = 16`为对称中心的子串右边界会落到R右侧，但在R右侧无法保证正确的对称性。此时只能知道`P[16] >= 5`，因为index到右边界R的距离为5。然后可以通过从`P[16] = 5`为初值向两侧扩展。  

这里是整个算法最核心的分支部分：

``` python
if P[i] <= R - i:
	P[i] = P[2 * C - i] # i' = 2 * C - i
else:
	P[i] = R - i
	expand from i with width P[i]
```

当第二种情况发生的时候，新的右边界已经在现有的R右侧，需要更新L，C，R三个边界变量，在上述情况中，`C = index = 16, R = 21`，在代码实现中不需要记录左边界L的值。

### Python实现
大部分Python实现只是简单地直接移植了其他语言的实现，这里给出一个比较Pythonic的实现方式：

``` python
def longest_palindrome(s):
    if not s:
        return 0
    t = '#'.join('^{}$'.format(s))
    n = len(t)
    p = [0] * n
    C = R = 0
    for i in range(1, n - 1):
        # here is the tricky part
        p[i] = (R > i) and min(R - i, p[2 * C - i])
        while t[i + p[i] + 1] == t[i - p[i] - 1]:
            p[i] += 1
        if i + p[i] > R:
            C, R = i, i + p[i]
    length, center = max((n, i) for i, n in enumerate(p))
    return s[(center - length) / 2: (center + length) / 2]

```

### 相关问题
这个算法在O(n)时间复杂度中求出了字符串中每个字符对应的最长回文长度，在很多相关问题中都可以发挥作用，附上几道遇到过的回文子串相关问题：

 - [Longest Palindrome](https://leetcode.com/problems/longest-palindromic-substring/)
 - [Shortest Palindrome](https://leetcode.com/problems/shortest-palindrome/)
 - [Count Palindrome]()