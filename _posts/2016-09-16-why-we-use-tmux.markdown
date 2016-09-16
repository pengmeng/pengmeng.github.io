---
title: '为什么要用Tmux'
guid: 8e2a718bb7e27746703b52f48bdb2aa3
comments: true
layout: post
tags:
     - tmux
     - 快捷键
---
### 为什么要用Tmux
ssh到服务器上的时候，有时需要打开多个ssh连接来进行多个操作，也有时需要跑一个比较耗时的任务，就需要持续保持ssh连接，这是很不方便的。使用Tmux的话这些问题就迎刃而解了！  
Tmux是一个终端窗口复用工具，用于在一个终端窗口中运行多个会话。而且可以随时切入后台运行和再接入。

### 安装Tmux
可以使用各种包管理工具或者从源码编译安装，Tmux有两个依赖库，如果没有的话需要一起安装。

#### 有root权限
在有root权限的情况下安装非常方便，以MAC OSX举例：  

``` shell
brew install tmux
```
一行命令解决所有问题，没有homebrew的话请自行安装。

#### 没有root权限
在公司的开发服务器上没有root权限的话，自己安装tmux比较麻烦，这里提供一个脚本，可以自动下载两个依赖和tmux源码包自动安装，一般来说可以直接搞定。  

``` shell
git clone git@gist.github.com:a4d00933394ebb0fbe15.git
sh tmux_local_install.sh
```

### 基本设置
tmux有一个独立的快捷键前缀，默认为`ctrl + b`。如果用类unix系统的话建议把`caps lock`映射为`ctrl`，这样的话手可以移动最短的距离来按`ctrl`。建议尽早改来熟悉一下。  
另外`ctrl + b`的组合按久了实在是有点累，建议改成`ctrl + a`，这样舒服很多。这个快捷键组合其实覆盖了MAC终端跳到行首的快捷键，所以设置`ctrl + a + a`来将这个快捷键组合送到bash。在配置文件`~/.tmux.conf`中加入如下设置：  

```
unbind C-b
set -g prefix C-a
bind C-a-a send-prefix
```

然后设置下使用vim按键模式：  

```
set-window-option -g mode-keys vi
```

其他设置可以暂时不用，或按需调整。

### 基本概念 & 操作

#### Pane
每次切分出来的一个新区域称为一个Pane，一个Pane是一个独立的bash。使用`C-a %`进行水平切分，`C-a "`进行垂直切分。  
如果想在Pane中跳转，使用`C-a [方向键]`，还有一些其他的快捷键可以跳转，但根据最小够用原则，记住最直观最有效的就好了。

#### Window
Window是Pane的一个集合，每个Window中可以随意组合Pane，`C-a c`创建一个新窗口，使用`C-a ,`修改当前窗口的名字。 
窗口有对应的编号，使用`C-a [窗口号]`切换到对应的窗口，或者使用`C-a w`列出当前会话下所有窗口。

#### Session
Session是多个Window的合集，也是tmux的直接操作单位。直接在命令行使用`tmux`可以创建一个默认Session，默认的Session名字是递增的编号，也可以使用命令`tmux new -s [session name]`来打开一个名字为`[session name]`的会话。默认会话可以通过`C-a $`来修改名字。  
使用`C-a s`列出当前tmux打开的所有会话，可以用上下键导航，Enter键激活。`C-a d`挂起当前会话到后台运行。  
未运行tmux时，可以使用`tmux ls`列出所有会话，`tmux attach -t [session name]`，简写`tmux a -t [session name]`。`tmux kill-session -t [session name]`关闭指定会话。

#### 退出
tmux内置了退出各个域的快捷键，但是还需要确认一下，感觉并不实用，直接退出特定的bash就自动关闭当前域了。  

### 复制粘贴
最开始用tmux的时候最头疼的就是复制粘贴了，现在终于学会了在一个会话中复制粘贴，还没实现复制到系统的粘贴板，之后再研究一下。  
`C-a [`开启粘贴模式（这个模式下可以通过vim的移动快捷键进行操作，可以翻看历史记录，对于没有PageUp/Down的键盘很实用），光标移动到要复制的地方之后按`空格`开始选择内容，此时同vim，选择完毕后按`Enter`键完成复制。在需要粘贴的地方使用`C-a ]`粘贴。

### Tmux外观设置
可以通过配置文件设置外观，但是太复杂了也懒得研究。我用了一个vim插件[tmuxline](https://github.com/edkolev/tmuxline.vim)将vim-airline的主题同步到了tmux，使用vim的时候还能达到视觉上的一致，强迫症福音。具体操作参考[vim学习笔记 - 2](http://pengmeng.me/2016/05/04/vim-study-note-2.html)

### 希望Tmux能改善大家的编码体验
还是那句话，最小够用原则，记住基本的快捷键，够用就行~