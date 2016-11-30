---
title: 'vim多文件浏览'
guid: dcfbde211c9c25c0f7169de19f1647e8
comments: true
layout: post
tags:
     - vim
---

### 概述
> A buffer is the in-memory text of a file. A window is a viewport on a buffer. A tab page is a collection of windows.   
> – vimdoc

vim支持buffer、window、tab三种形式（层级）的多文件浏览，分别对应缓冲区、窗口、标签页。从vimdoc可以看出，三个层级依次升高，本文将从tab开始，依次递减来介绍三种形式的多文件浏览功能及操作。

### tab

#### 以tab形式打开多个文件

``` shell
vim -p .bashrc .vimrc
```

#### 打开新的tab
``` vim
:[count]tabe[dit] [++opt] [+cmd] {file}
:[count]tabnew [++opt] [+cmd] {file}
" [count]表示在第[count]个tab后打开新的tab
" e.g. ':0tab file' 新tab将作为第一个tab出现
```

#### 关闭tab
``` vim
:tabc[lose][!]
" close current tab
:tabc[lose][!] {count}
" close tab page {count}
:tabo[nly][!]
" close all other tab pages
:{count}tabo[nly][!]
" close all tabs except the {count}th one
```

#### 在tab之间切换
``` vim
:tabn[ext] {count}
{count}gt
" Go to tab page {count}.  The first tab page has number one.
:tabp[revious] {count}
:tabN[ext] {count}
{count}gT
" Go {count} tab pages back.
:tabr[ewind]
:tabfir[st]
" Go to the first tab page
:tabl[ast]
" Go to the last page
```

#### 移动tab
``` vim
:tabs
" List the tab pages and the windows they contain.
:[N]tabm[ove]
" Move the current tab page to after tab page N
:0tabm
:tabm 0
" move the tab to the first
:tabm
:$tabm
:tabm $
" move the tab to the last
:-tabm
:tabm -
:tabm -1
" move the tab to the left
:+tabm
:tabm +
:tabm +1
" move the tab to the right
```

### window

#### 以window形式打开多个文件

``` shell
vim -O file1 file2
# 水平分割
vim -o file1 file2
# 垂直分割
```

#### 打开新的window
``` vim
CTRL-W s
:[N]sp[lit] [++opt] [+cmd] {file}
" split current window into two horizontally, open a new file or load buffer if {file} given
CTRL-W n
:[N]new [++opt] [+cmd] {file}
" as above, but editing an empty file in new window or same as above if {file} given
CTRL-W v
:[N]vs[plit] [++opt] [+cmd] {file}
" as above but vertically
:[N]vne[w] [++opt] [+cmd] {file}
" as above

:[N]sv[iew] [++opt] [+cmd] {file}
Same as ":split", but set 'readonly' option for this buffer.
```

#### 关闭一个window
``` vim
:q[uit]
:{count}q[uit]
CTRL-W q
" Without {count}: Quit the current window. If {count} is given quit the {count} window.
CTRL-W c
:{count}clo[se][!]
" as above
:{count}hid[e]
" Quit the current window, unless it is the last window on the screen. The buffer becomes hidden.
CTRL-W o
:{count}on[ly][!]
" Make the current window the only one on the screen. All other windows are closed.
```

#### 切换窗口
``` vim
CTRL-W <方向键>
" move cursor to a direction
```

#### 其他
移动窗口和调整窗口大小两个功能不是特别常用，如果需要的话请参考`:h window-moving` & `:h window-resize`

### buffer
一般来说，vim大神都用buffer，虽然我也不知道为啥。但buffer应该是最高效但是最不直观的多文件编辑方式了。

#### 以buffer形式打开多个文件
直接使用vim打开多个文件即可

``` shell
vim file1 file2 ...
```

#### 显示所有buffer
``` vim
:files[!] [flags]
:buffers[!] [flags]
:ls[!] [flags]
" Show all buffers, for details about [flags], refer to :h buffers
```

#### 打开新的buffer
``` vim
:e[dit] {file}
" Edit the current file if {file} not given, useful to re-edit current file when it has been changed outside vim.
:e[dit]! {file}
" Edit the current file if {file} not given, discarding any changes to current buffer.
:e[dit] #[count]
" Edit the [count]th buffer.
:fin[d][!] {file}
" Find {file} in 'path' and edit it.
:{count}fin[d][!] {file}
" Like :find, but use {count}th match in 'path'.
:bad[d] [+lnum] {fname}
Add file name {fname} to the buffer list, without loading it. If "lnum" is specified, the cursor will be positioned at that line when the buffer is first entered.
```

#### 关闭buffer
``` vim
:[N]bd[elete][!]
:bd[elete][!] [N]
:bdelete[!] {bufname}
:bdelete[!] N1 N2 ...
:N,Mbdelete[!]
" Unload buffer with given [N] number or {bufname} name, if buffer name is a number, please use [N] instead.

:[N]bun[load][!]
:bun[load][!] [N]
:bunload[!] {bufname}
:bunload[!] N1 N2 ...
:N,Mbunload[!]
" Unload buffer with given [N] number or {bufname} name. The memory allocated for this buffer will be freed. The buffer remains in the buffer list.
```

#### buffer间跳转
vim 支持 `:b *.py<tab>` 方式的通配符匹配，使用`<tab>`在匹配列表中跳转，回车选中buffer。

``` vim
:[N]b[uffer][!] [+cmd] [N]
:[N]b[uffer][!] [+cmd] {bufname}
" Edit [N]th or {bufname} buffer in the buffer list. [N] defaults to one.

:[N]bn[ext][!] [+cmd] [N]
" Go to [N]th next buffer in buffer list.  [N] defaults to one. Wraps around the end of the buffer list.

:[N]bN[ext][!] [+cmd] [N]
:[N]bp[revious][!] [+cmd] [N]
" Go to [N]th previous buffer in buffer list.  [N] defaults to one. Wraps around the start of the buffer list.

:br[ewind][!] [+cmd]
:bf[irst] [+cmd]
" Go to first buffer in buffer list.
:bl[ast][!] [+cmd]
" Go to last buffer in buffer list.
```

#### 快捷键配置
映射了两个buffer跳转的快捷键  

``` vim
nnoremap [b :bp<CR>
nnoremap ]b :bn<CR>
```

### 总结
这次介绍了vim进行多文件编辑的几种方式，其实比较实用的还是buffer形式的多文件操作，tab和window用起来并不方便，不如直接用tmux进行分屏。  
如果安装了`vim-airline`插件，顶端会显示tab/buffer打开情况，这个有一点误导，因为buffer其实理论上是不可见的。