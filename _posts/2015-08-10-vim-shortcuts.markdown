---
title: 'Vim 快捷键总结'
guid: fb7fb1ceb693567bc17fb2506877448b
comments: true
layout: post
tags:
     - Vim
     - cheatsheet
     - 快捷键
---
[参考](http://blog.csdn.net/ceven2010/article/details/7406341)

### 移动
 - `Ctrl f` `Ctrl b` 向下、上翻页（tmux环境下需要按两次）
 - `Ctrl d` `Ctrl u` 向下、上翻半页
 - `$` 行尾	`0`行首
 - `w` 下一个词 `b` 上一个词
 - `(` `)` 移动到上、下一句（在代码文件中表现不确定）
 - `{` `}` 移动到上、下一段（在代码文件中表现为代码块）
 - `gg` 首行 `G` 尾行
 - `%` 在匹配括号间跳转
 - `nG` 跳到第n行
 - `H` 屏幕顶部 `M` 屏幕中间 `L` 屏幕底部

### 查找
 - `*` 向后搜索当前词
 - `#` 向前搜索当前词
 - `/` 向文件尾搜索
 - `?` 向文件头搜索
 -  `n` 下一条命中 `N` 上一条命中
 - `:nohl` 取消高亮

### 替换
 - `:s/s1/s2` 将当前光标后第一个s1替换为s2
 - `:%s/s1/s2` 将全部s1替换为s2
 - `:s/s1/s2/g` 将当前行中s1替换为s2
 - `:n1, n2 s/s1/s2/g` 将n1, n2行之间的s1替换为s2

### 编辑
 - `a` 光标后插入 `A` 行尾插入
 - `i` 光标前插入 `I` 行首插入
 - `o` 当前行后插入新行 `O` 当前行前插入新行
 - `s` 删除光标所在字符并插入 `S` 删除当前行并插入
 - `dd` 删除行 `cc` 删除并插入（同`S`）
 - `dw` 删除词 `cw` 删除词并插入
 - `dl` or `x` 删除字符 `cl` 删除字符并插入（同`s`）
 - `D` 删除至行尾 `C` 删除至行尾并插入
 - `yy` or `Y` 复制行
 - `p` 粘贴之前删除或复制的内容

	```
	xp 交换字符
	ddp 交换行
	dwp 交换词
	yyp 重复当前行
	```
 - `u` 撤销 `Ctrl r` redo
 - `>>` `<<` 缩进、反缩进选中的代码
 - `J` 合并当前行和下一行

### Visual模式
 - `v` 进入字符Visual模式 `V` 进入行Visual模式 `Ctrl v` 进入块Visual模式
 - `d` 删除选中内容 `c` 删除选中内容并插入

### 窗口（推荐使用tmux代替）
 - `:split` 垂直分割 `:vsplit` 水平分割
 - `:new` 垂直打开新窗口 `:vnew` 水平打开新窗口
 - `:sf file` 在新窗口打开`file`
 - `:exit` or `:close` 关闭当前窗口
 - `:only` 关闭所有其他窗口
 - `Ctrl w, 方向键` 切换窗口
 - `Ctrl t` top `Ctrl b` bottom

### 命令
 - `:n1, n2 del` or `:n1, n2 d` 删除n1, n2之间的内容
 - `:n1, n2 move n3` or `:n1, n2 mo n3` 将n1, n2之间的内容移动到n3下一行
 - `:n1, n2 copy n3` or `:n1, n2 co n3` 将n1, n2之间的内容移动到n3下一行
 - `:n1, n2 >` `n1, n2 <` n1, n2之间的内容缩进、反缩进
 - `:q` 退出 `:w` 写入 `:wq` or `:x` 写入并退出
 - `:!command` 执行shell command
 - `:n1, n2 w!command` 将n1, n2之间的内容作为command的输入并执行
 - `:r!command` 将command的执行结果写入当前行
 - `:shell` 开启一个默认shell

### 宏
 - `q [a..z]` 录制操作存入寄存器`[a..z]` `q` 结束录制
 - `@ [a..z]` 执行寄存器`[a..z]`中的操作
 - `@ @` 执行最近一次宏操作
