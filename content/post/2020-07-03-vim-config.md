---
title: "vim配置文件"
date: 2020-07-03T16:41:31+08:00
---

Vim 是最重要的编辑器之一，主要有下面几个优点。
* 可以不使用鼠标，完全用键盘操作。
* 系统资源占用小，打开大文件毫无压力。
* 键盘命令变成肌肉记忆以后，操作速度极快。
* 服务器默认都安装 Vi 或 Vim。

Vim 的全局配置一般在/etc/vim/vimrc或者/etc/vimrc，对所有用户生效。用户个人的配置在~/.vimrc。
下面是一个参考的配置文件：

```
set sw=4
set ts=4
set et
set smarttab
set smartindent
set lbr
set sm
set selection=inclusive
set wildmenu

au FileType php setlocal dict+=~/.vim/dict/php_funclist.dict

syntax on
set cul
set cuc
set shortmess=atI
color ron
"autocmd InsertEnter
set ruler
set showcmd

set scrolloff=3
set statusline=%F%m%r%h%w\ [FORMAT=%{&ff}]\ [TYPE=%Y]\ [POS=%l,%v][%p%%]\ %{strftime(\"%d/%m/%y\ -\ %H:%M\")}   "状态行显示的内容 
set laststatus=2
set nocompatible

set cindent
set tabstop=4
set shiftwidth=4
set expandtab
set number
set history=1200
set hlsearch
set incsearch
set langmenu=zh_CN.UTF-8
set helplang=cn
set cmdheight=2
filetype on

nmap tt :%s/\t/    /g<CR>
```
