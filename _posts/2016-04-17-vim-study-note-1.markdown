---
title: 'vim学习笔记 - 1'
guid: f56344674a11664ef187c7526589c626
comments: true
layout: post
tags:
     - vim
---

### 写在前面
这两天没事把vim重新整顿了一下，一共加起来花了估计8小时的时间，基本上从零开始到初具雏形。打算把完整过程记录下来供大家参考。  

附上我的vim的[Github repo](https://github.com/pengmeng/crazy-vim)，包含我现在使用的配置文件和所有插件。

最开始写代码的时候，被大神安利vim，当时真的是纯IDE党，对学习曲线这么陡峭的vim实在是没有兴趣。后来需要在服务器上写代码，必须学习vim，于是初步学习了一下基本配置和操作，算是有了个勉强美观可用的版本。这几天无聊就觉得重新配置一下vim，把vim打造成了Python IDE，先上一张最终效果图：  

![](/media/files/2016/04/vim-preview.png)  

整个笔记分几篇博客，从最开始的安装，到插件选择、配置，vimrc配置等等。  
操作环境：  

 - MAC OS X EI Capitan (10.11.4)
 - vim 7.4

不过吧，最终目的都是为了提高开发效率，vim的学习曲线还是相当陡峭的，IDE党可以直接跳到本文最后一句话！

### 安装
安装有很多方法，我这里假设已经有了`homebrew`，如果还没有，赶紧装！直接安装：

```
$ brew install vim --with-lua --with-python
```
然后验证安装正确性：

```
$ vim --version
```
能打印版本信息和feature列表则安装正确。

### 配置文件
vim所有的配置依赖于`.vimrc`配置文件，如果第一次使用的话要在home目录建立这个文件：

```
$ touch ~/.vimrc
```
另外还有一个文件夹用于vim插件、配色方案、语法文件等的存放，我忘了这个是不是自动生成的，如果没有的话就手动创建一下吧，最后目录结构是：

```
$ tree -L 1 ~/.vim
.vim
│
├── bundle
├── colors
└── syntax

3 directories, 0 file
```
其中`bundle`是插件，`colors`是配色方案，`syntax`是语法文件。

### 插件管理
vim的一个强大之处就是插件系统，无数大神写了无数好用的插件造福人类！所以我们需要先有一个插件管理方案。我之前用的是`pathogen`，很简单粗暴的插件管理系统，但是当插件多了之后想临时关闭某个插件不是很方便，也可能是我当时没掌握方法。这次选用了大部分插件都推荐的一个方案[`Vundle`](https://github.com/VundleVim/Vundle.vim)，这个软件用起来很方便，基本配置也很简单。

```
$ git clone git@github.com:VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```
然后把Vundle repo里面推荐的配置复制到`.vimrc`中，我删减了一部分，只留下了关键配置：

``` vim
"" Configuration for Vundle
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'VundleVim/Vundle.vim'
" More plugin goes here

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
filetype plugin on
filetype on
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal

"" END Vundle
```

之后如果需要使用新的插件只需要增加`Plugin`并执行`:PluginInstall`即可。现在我们已经有了配置文件和插件管理系统，可以开始配置vim的后续活动了。

### 基本配置
现在的vim没有任何配置，看起来很丑，所以先进行一些基本配置，具体请参考我的[`.vimrc`](https://github.com/pengmeng/crazy-vim/blob/master/.vimrc)文件中`General Configuration`中`functional`和`indent`部分。这进行了基本的行号显示、语法高亮、编码、搜索方式、缩进等等设置，具体某个设置不知道可以Google。这段设置也是我很早之前从别人那里粘贴来的，出处已不可考。这里贴一个demo，之后更新以repo中为准：

``` vim
"functional
let mapleader=";"
set encoding=utf-8
set clipboard=unnamed " 支持系统剪贴板

set shortmess=atI
autocmd BufEnter * :syntax sync fromstart

set showcmd         " 显示命令
set lz              " 当运行宏时，在命令执行完成之前，不重绘屏幕
set hid             " 可以在没有保存的情况下切换buffer
set backspace=eol,start,indent
set whichwrap+=<,>,h,l " 退格键和方向键可以换行
set incsearch       " 增量式搜索
set hlsearch        " 高亮搜索
set magic           " 额，自己:h magic吧，一行很难解释
set showmatch       " 显示匹配的括号                                                                                                                                                 
set nobackup        " 关闭备份
set nowb
set noswapfile      " 不使用swp文件，注意，错误退出后无法恢复
set lbr             " 在breakat字符处而不是最后一个字符处断行
set wildmenu
set nofen
set fdl=10
set foldlevel=99

"indent
filetype indent on
set autoindent
set copyindent
set expandtab
set smarttab
set shiftwidth=4
set tabstop=4
set softtabstop=4

"appearance
set shortmess=atI
set nu
set ruler
set cursorline
syntax enable
syntax on
set t_Co=256
set background=dark
```

### 基本操作
现在我们有了配置过的vim，虽然丑一点，但还是可以稳定工作的，下一步就要学习vim的基本操作了。vim的操作和各种快捷键组合简直复杂，基本上可以写一篇独立的博客来说了。之前一篇[Vim 快捷键总结](http://pengmeng.me/2015/08/10/vim-shortcuts.html)做了基本操作的总结，这里再推荐Stack Overflow上的帖子[What are the most-used vim commands/keypresses?](http://stackoverflow.com/questions/5400806/what-are-the-most-used-vim-commands-keypresses/5400978#5400978)。

### 编辑/查看多个文件
vim提供了window，buffer，tab三种方式进行单个文件的多窗口浏览以及编辑多个文件的功能。三个有联系也有区别，这一部分之后单独写一篇学习笔记。  
一个很广泛的说法就是，vim大神都用buffer。  
[学习笔记敬请期待]()

### 下期预告
下一篇学习笔记中将介绍最开始提到的各种插件的安装和基本使用。

### 最后一句话
文章开头提到了IDE党请直接跳到最后一句话：  
**配置整个vim用了大概8小时时间，有这时间我为啥不直接用IDE呢！（小s冷漠）**