---
title: vim学习笔记-vim基础配置
tags:
  - vim
  - vundle
  - vimrc
date: 2018-05-12 23:59:41
---


准备使用vim作为主要开发工具，这里记录下vim的学习配置过程。本篇配置的相关东西主要为了实现markdown语法高亮，和一些vim的基础配置。

<!--more-->

## vundle的介绍以及安装

应为vim本身缺乏有效的插件管理工具，插件一般分布于`~/.vim`下面的几个文件夹里面，插件的安装与更新主要依靠自己手动进行，这里发现可以使用vundle进行统一管理。

### vundle介绍

vundle可以实现下面功能：
	1. 在`.vimrc`中跟踪和管理插件
	2. 安装特定格式的插件
	3. 更新特定格式的插件
	4. 通过插件名称搜索vim scripts中的插件
	5. 清理未被使用过的插件

### vundle安装

运行下面的程序将代码clone到对应的文件夹中。

```shell
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

配置vimrc，这里官方给出的配置文件配置内容如下：

```
set nocompatible              " 去除VI一致性,必须要添加
filetype off                  " 必须要添加

" 设置包括vundle和初始化相关的runtime path
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" 另一种选择, 指定一个vundle安装插件的路径
"call vundle#begin('~/some/path/here')

" 让vundle管理插件版本,必须
Plugin 'VundleVim/Vundle.vim'

" 以下范例用来支持不同格式的插件安装.
" 请将安装插件的命令放在vundle#begin和vundle#end之间.
" Github上的插件
" 格式为 Plugin '用户名/插件仓库名'
Plugin 'tpope/vim-fugitive'
" 来自 http://vim-scripts.org/vim/scripts.html 的插件
" Plugin '插件名称' 实际上是 Plugin 'vim-scripts/插件仓库名' 只是此处的用户名可以省略
Plugin 'L9'
" 由Git支持但不再github上的插件仓库 Plugin 'git clone 后面的地址'
Plugin 'git://git.wincent.com/command-t.git'
" 本地的Git仓库(例如自己的插件) Plugin 'file:///+本地插件仓库绝对路径'
Plugin 'file:///home/gmarik/path/to/plugin'
" 插件在仓库的子目录中.
" 正确指定路径用以设置runtimepath. 以下范例插件在sparkup/vim目录下
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" 安装L9，如果已经安装过这个插件，可利用以下格式避免命名冲突
Plugin 'ascenator/L9', {'name': 'newL9'}

" 你的所有插件需要在下面这行之前
call vundle#end()            " 必须
filetype plugin indent on    " 必须 加载vim自带和插件相应的语法和文件类型相关脚本
" 忽视插件改变缩进,可以使用以下替代:
"filetype plugin on
"
" 常用的命令
" :PluginList       - 列出所有已配置的插件
" :PluginInstall     - 安装插件,追加 `!` 用以更新或使用 :PluginUpdate
" :PluginSearch foo - 搜索 foo ; 追加 `!` 清除本地缓存
" :PluginClean      - 清除未使用插件,需要确认; 追加 `!` 自动批准移除未使用插件
"
" 查阅 :h vundle 获取更多细节和wiki以及FAQ
" 将你自己对非插件片段放在这行之后
```

### vundle插件管理

vim的插件主要有下面三种类型：
	1. Github上面vim-scripts仓库的插件：只需要写出仓库名称即可
	2. 在Github上面的非vim-scripts仓库的插件：需要写出“用户名/仓库名称”
	3. 不在GitHub上面的插件。


其他相关资料参考[vundle readme文件](https://github.com/VundleVim/Vundle.vim).

## 配置markdown语法高亮

这里采用[vim-markdown](https://github.com/plasticboy/vim-markdown)做语法高亮，具体安装过程参考自官方readme文件。

在vimrc中添加下面两行代码：

```
Plugin 'godlygeek/tabular'
Plugin 'plasticboy/vim-markdown'
```

然后在vim中输入下面代码

```
:so ~/.vimrc
:PluginInstall
```

## vimrc配置

这里参考这篇[博客](https://blog.csdn.net/chuanj1985/article/details/6873830)中的解释进行理解

到目前为止我的vimrm如下，并对代码做出了注释：

```
" --- Gotta be first ---
set nocompatible											" 去除VI一致性，必须添加

" --- set number ---
set nu!																" 显示行号
syntax on															" 语法高亮
set ruler															" 设置在编辑过程中，在右下角显示光标位置的状态行

" --- set clipboard to XA_PRIMARY

" --- 2 space tab ---
set autoindent												" 设置自动对齐，也就是将当前行对齐的格式应用到下一行
set cindent														" 设置针对c语言的自动缩进
set tabstop=2													" 设置tab键为2个空格
set shiftwidth=2											" 设置当行之间交错时使用2个空格
set ai!																" 设置自动缩进
set showmatch													" 设置匹配模式，类似输入一个左括号匹配右括号

" --- vundle plugin manager ---
filetype off		     									" 必须添加

set rtp+=~/.vim/bundle/Vundle.vim 		" 设置包括vundle和初始化相关的runtime path
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'					" 让vundle管理插件版本，必须
" alternatively, pass a path where Vundle should install plugins
" call vundle#begin('~/some/path/here')

Plugin 'godlygeek/tabular'						" 设置markdown语法高亮
Plugin 'plasticboy/vim-markdown'

" All of your Plugins must be added before the following line
call vundle#end()
```

