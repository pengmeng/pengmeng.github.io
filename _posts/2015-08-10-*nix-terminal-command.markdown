---
title: '*nix 终端快捷键 (Mac OSX 环境下测试)'
guid: 8367c79e894c82e06fda36885bcec177
comments: true
layout: post
tags:
     - Mac OSX
     - Linux
     - Unix
     - 终端
     - 快捷键
---

### Mac OSX 环境下的终端快捷键（持续更新）
***Alt起始的命令需要在偏好设置中勾选`Use Option as Meta key`***  

#### 跳转

 - `Ctrl a` 跳到命令开始  
 - `Ctrl e` 跳到命令结束
 - `Alt f` or `Alt right` 跳到下一个单词
 - `Alt b` or `Alt left` 跳到上一个单词
 - `Ctrl x x` 在行首和光标之间跳转
 - `Ctrl l` 清屏（相当于clear命令）

#### 编辑

 - `Ctrl d` 删除光标所在处字符
 - `Ctrl h` 删除光标前一个字符
 - `Alt d` 删除光标后一个词
 - `Ctrl w` or `Alt delete` 删除光标前一个词
 - `Ctrl u` 删除光标之前的内容（不包含光标）
 - `Ctrl k` 删除光标之后的内容（包含光标）
 - `Ctrl y` 粘贴刚才删除的字符
 - `Ctrl t` 互换光标所在字符与前一个字符，光标后移一个字符
 - `Alt t` 互换当前词与前一个词
 - `Ctrl x u` 撤销
 - `Alt u` 把当前词转化为大写
 - `Alt l` 把当前词转化为小写
 - `Alt c` Capitalize
 - `Ctrl c` 删除整行

#### 历史命令

 - `Ctrl r` 搜索历史命令
 - `Ctrl g` 退出搜索
 - `history` 列出历史命令
 - `!n` 执行第n条历史命令
 - `!?s?` 执行最近一条含有s的命令
 - `!!` 重复执行上一条命令

	```
	> mkdir /an-useless-dir/
	mkdir: /an-useless-dir/: Permission denied
	> sudo !!				#以sudo执行上一条命令
	```
 - `!$` 上一条命令最后一个的参数

	```
	> cat /etc/hosts		#查看hosts文件
	> vim !$				#使用vim编辑hosts文件
	```
 - `!*` 上一条命令所有参数