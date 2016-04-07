---
title: 'Binary Search 二分查找总结'
guid: 7bace1c8b5fd45e1d12cc6aba9bf8499
comments: true
layout: post
tags:
     - python
     - algorithm
     - 算法
     - 二分
---

### 前面

二分查找作为基本的算法，实现难度不高，但是写出完全bug free的代码其实还是有挑战性的。常见bug有死循环、无法找到target元素等。作为算法渣还是好好总结一下以便记忆。  
之前Google到了一篇[总结的很细致的文章](http://simpleandstupid.com/2014/12/23/关于二分查找的总结/)，但是感觉里面总结的有点太复杂了。想到更好的实现肯定在各种语言的标准库中，于是参考了Python标准库中[bisect包的源码](https://hg.python.org/cpython/file/3.3/Lib/bisect.py)。这个实现应该是比较权威的，总结如下。

### 最简二分查找

升序数组中查找一个target值，`无重复元素`，找到target则返回该元素对应的index，找不到则返回插入位置。

``` python
def binary_search(nums, target):
    start, end = 0, len(nums) - 1
    while start <= end:	#key point1
        mid = (start + end) >> 1
        if nums[mid] > target:
            end = mid - 1		#key point2
        elif nums[mid] < target:
            start = mid + 1		#key point3
        else:
            return mid
    return start
```

### 变形一

升序数组中查找一个target值的`左`插入位置，`有重复元素`，如果target出现在数组中，返回最左侧target的index，如果target不存在于数组中，返回小于target的最大元素的index。即符合：  

``` python
all(x < target for x in l[:i]) and all(x >= target for x in  l[i:]) is True
```

```python
def binary_search_left(nums, target):
	start, end = 0, len(nums)
	while start < end:
		mid = (start + end) >> 1
		if nums[mid] < target:
			start = mid + 1
		else:
			end = mid
	return start
```

### 变形二

升序数组中查找一个target值的`右`插入位置，如果target出现在数组中，返回最右侧target下一个元素的index，如果target不存在于数组中，返回大于target的最小元素的index。即符合：

``` python
all(x <= target for x in l[:i]) and all(x > target for x in l[i:]) is True
```

``` python
def binary_search_right(nums, target):
	start, end = 0, len(nums)
	while start < end:
		mid = (start + end) >> 1
		if nums[mid] > target:
			end = mid
		else:
			start = mid + 1
	return start
```

### 最后

 - 有的语言中需要考虑大数overflow的问题，所以求mid的表达式要写成`mid = start + (end - start) / 2`。  
 - 后两种变形只是最简实现，如果需要找不到返回-1的话需要在最后加入判断过程，以及变形二中最后返回的并不是target的index，都需要适当调整。