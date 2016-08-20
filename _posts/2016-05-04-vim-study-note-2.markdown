---
title: 'vim学习笔记 - 2'
guid: 44619b71e449993b6366e2ac97f74749
comments: true
layout: post
tags:
     - vim
---

### 前情提要
在学习笔记-1中，对vim进行了基本的配置，学习了基本的使用方法和快捷键，目前vim已经是一个可以满足简单文本编辑功能的编辑器，但是很丑而且效率不高。为了让vim更加美观并且提高编码效率，这一篇笔记将介绍一些特别好用的vim插件。按照插件的类型进行分类介绍。

### 插件管理
我目前用的插件管理工具是Vundle，在第一篇文章中已经介绍过了。所有插件通过Vundle从Github安装。安装格式举例：

``` vim
Plugin 'vim-airline/vim-airline'
```
这个插件的Github路径为`http://github.com/vim-airline/vim-airline`。

### 外观类插件

#### vim-airline  
vim-airline是现在使用最为广泛的vim外观插件，是powerline的改良版，使用了powerline中的powerfont字体。该插件提供了可配置的状态栏，可以在状态栏显示许多有用的信息。效果图：  
![](/media/files/2016/05/vim-airline.png)  
vim-airline内置了常用的主题，其他的主题可以通过安装vim-airline-themes获得。  

安装：

``` vim
Plugin 'vim-airline/vim-airline'  
Plugin 'vim-airline/vim-airline-themes'
```

配置：  

``` vim
let g:airline_theme="gruvbox"
let g:airline_powerline_fonts=1
set laststatus=2
let g:airline#extensions#tabline#enabled = 1 
```

#### gruvbox  
gruvbox是一个独立的vim主题，以插件的形式发布，目前我用的就是这个主题，vim配色和字体配色都很理想，我甚至把Mac terminal配置成了和这个主题一致的配色。并且这个主题可以用于vim-airline，参考前一个插件的配置部分。效果图：  
![](/media/files/2016/05/vim-gruvbox.png)  

安装&配置：  

``` vim
let g:gruvbox_contrast_dark='hard'
Plugin 'morhetz/gruvbox'
```

这里的配置必须写在`Plugin`之前，否则无法生效。  
vim主题配置：

``` vim
colorscheme gruvbox
```


#### tmuxline  
tmuxline是一个将powerline字体和vim-airline主题配色带到tmux状态栏的一个插件，如果使用tmux而且有强迫症的话可以考虑安装。效果图：  
![](/media/files/2016/05/vim-tmuxline.png)  

安装：

``` vim
Plugin 'edkolev/tmuxline.vim'
```

配置：

``` vim
let g:airline#extensions#tmuxline#enabled = 0
```

命令：  

``` vim
:Tmuxline airline
" or
:Tmuxline airline_insert
" or
:Tmuxline airline_visual
```

我选择的是第一个命令，直接将vim normal模式的配色设置到tmux中。  


### 自动补全插件
自动补全一直都是我坚持用IDE的主要理由，好的自动补全功能可以极大提高效率。

#### YouCompleteMe  
vim下最强大的自动补全插件之一，支持各种常见语言的语法补全，分别使用各种语言专有的补全引擎，算是一个补全引擎的集合。效果图：  
![](/media/files/2016/05/vim-ycm.png)  

安装：  
YouCompleteMe需要预先编译，Github页面上有很详细的安装教程，这里只贴出Mac的安装方法。

``` shell
cd ~/.vim/bundle/YouCompleteMe
	
```

``` vim
Plugin 'Valloric/YouCompleteMe'
```

配置：  
VimTip1228中还有另外两个键盘映射，但是在Mac OSX环境下会有bug，所以这里只保留了一个回车后关闭补全窗口的映射。

``` vim
let g:ycm_complete_in_comments=1
let g:ycm_complete_in_strings = 1
let g:ycm_collect_identifiers_from_comments_and_strings=0
let g:ycm_confirm_extra_conf=0
let g:ycm_collect_identifiers_from_tags_files=1
let g:ycm_min_num_of_chars_for_completion=2
let g:ycm_seed_identifiers_with_syntax=1
let g:ycm_autoclose_preview_window_after_completion=1
set completeopt=longest,menu,preview
let g:ycm_key_list_select_completion = ['<Down>']
let g:ycm_key_list_previous_completion = ['<Up>']
"autocmd InsertLeave * if pumvisible() == 0|pclose|endif
" change key map (VimTip1228)
inoremap <expr> <CR>       pumvisible() ? "\<C-y>" : "\<CR>"
```


#### UltiSnips & vim-snippets  
UltiSnips是一个可以自动补全一些常用代码片段的插件，拥有很多实用的特性而且速度很快。但它只是一个补全引擎，实际的代码片段在vim-snippets repo中。效果图：  
![](/media/files/2016/05/vim-snip.png)  

安装：  
 	
``` vim
Plugin 'SirVer/ultisnips'                                                                                                                                                            
Plugin 'honza/vim-snippets'
```

配置：  
使用tab键进行代码片段的选取

``` vim
let g:UltiSnipsExpandTrigger="<tab>"
let g:UltiSnipsJumpForwardTrigger="<tab>"
let g:UltiSnipsJumpBackwardTrigger="<s-tab>"
```


#### delimitMate  
这是一款小巧但极其实用的插件，只实现了一个功能，就是各种括号的补全。功能简单明了这里就不上图了。

安装：  
	
``` vim
Plugin 'Raimondi/delimitMate'
```


#### NERD Commenter  
除了各种自动补全，智能注释也是很提高效率的一个功能，我个人很喜欢IDE里面各种一键注释的快捷键。  

安装：  
	
``` vim
Plugin 'scrooloose/nerdcommenter'
```

配置：  
	
``` vim
let g:NERDSpaceDelims=1
```


#### indentpython.vim  
一个小的Python缩进辅助插件。  
安装：  

``` vim
Plugin 'vim-scripts/indentpython.vim'
```


### 功能性插件
功能性插件主要包括非代码类功能增强插件，例如文件树、全局搜索、语法检查等等。

#### The NERD Tree  
这个插件模拟了IDE左侧的工程目录，以树形结构展示了当前的文件系统。效果图：  
![](/media/files/2016/05/vim-nerdtree.png)  

安装：  

``` vim
Plugin 'scrooloose/nerdtree'
```
		
#### Tagbar: a class outline viewer for Vim  
如英文描述，tagbar是一个展示当前文件中代码结构的插件，例如变量、函数、类以及类成员等等。效果图：  
![](/media/files/2016/05/vim-tagbar.png)  

安装：  

``` vim
Plugin 'majutsushi/tagbar'
```


#### Syntastic  
语法检查插件，能提供各种语言的语法检查、定位和高亮显示等功能。效果图：  
![](/media/files/2016/05/vim-syntastic.png)  

安装：  

``` vim
Plugin 'scrooloose/syntastic'
```

配置：
  
``` vim
set statusline+=%#warningmsg#
set statusline+=%{SyntasticStatuslineFlag()}
set statusline+=%*
	
let g:syntastic_always_populate_loc_list = 1 
let g:syntastic_auto_loc_list = 1                                                                                                                                                    
let g:syntastic_check_on_open = 1 
let g:syntastic_check_on_wq = 0 
	
let g:syntastic_error_symbol = "✘" 
let g:syntastic_warning_symbol = "△" 
```


#### ctrlp.vim  
一个全局搜索插件，引用官方介绍`Full path fuzzy file, buffer, mru, tag, . finder for Vim.`。效果图：  
![](/media/files/2016/05/vim-ctrlp.png)  

安装：  

``` vim
Plugin 'ctrlpvim/ctrlp.vim'
```

配置：  
这几个配置主要是设置了忽视哪些文件夹。

``` vim
let g:ctrlp_custom_ignore = '\v[\/]\.(git|hg|svn)$'
let g:ctrlp_custom_ignore = { 
  \ 'dir':  '\v[\/]\.(git|hg|svn)$',                                                                                                                                                 
  \ 'file': '\v\.(log|jpg|exe|so|dll)$',
  \ }
```


#### SimpylFold  
Python代码折叠插件，貌似这个比较适用于Python，其他语言肯定也有相应的插件。效果图：  
![](/media/files/2016/05/vim-fold.png)  

安装：  

``` vim
Plugin 'tmhedberg/SimpylFold'
```

配置：  

``` vim
let g:SimpylFold_docstring_preview = 1 
let g:SimpylFold_fold_import = 0 
autocmd BufWinEnter *.py setlocal foldexpr=SimpylFold(v:lnum) foldmethod=expr
autocmd BufWinLeave *.py setlocal foldexpr< foldmethod<
```


#### Startify  
vim启动页面增强！又萌又实用的插件。效果图：  
![](/media/files/2016/05/vim-startify.png)  

安装：  

``` vim
Plugin 'mhinz/vim-startify'
```


#### fugitive.vim  
将git命令封装、集成在vim中的插件。  

安装：  

``` vim
Plugin 'tpope/vim-fugitive'
```


### 下期预告
这篇主要介绍了打造IDE过程中用到的一下插件和安装配置过程，具体的插件使用方法之后再单开文章介绍。下一期学习笔记比较简短，主要包括了vim功能性的配置，例如常用快捷键绑定、如何设置相对行号等。