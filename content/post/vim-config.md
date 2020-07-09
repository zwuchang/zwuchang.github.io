---
title: "vim配置文件"
date: 2020-07-03T16:41:31+08:00
url: "vim_config.html"
---

记录一下使用过的一个vim配置
<!--more-->
Vim 是最重要的编辑器之一，主要有下面几个优点。
* 可以不使用鼠标，完全用键盘操作。
* 系统资源占用小，打开大文件毫无压力。
* 键盘命令变成肌肉记忆以后，操作速度极快。
* 服务器默认都安装 Vi 或 Vim。

## 配置参考
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
set nocompatible "突出当前行

set cindent
set tabstop=4 " 设置Tab长度为4空格
set shiftwidth=4 " 设置自动缩进长度为4空格
set expandtab
set number
set history=1200
set hlsearch
set incsearch
set langmenu=zh_CN.UTF-8
set helplang=cn
set cmdheight=2
set pastetoggle=<F9>  "切换paste开关的选项
nmap tt :%s/\t/    /g<CR>

syntax on " 开启文件类型侦测
filetype plugin indent on    " 启用自动补全
" 退出插入模式指定类型的文件自动保存
au InsertLeave *.go,*.sh,*.php write
au FileType php setlocal dict+=~/.vim/dict/php_funclist.dict

```

## 粘贴模式
在windows复制某段代码进vim时，可能会导致原有缩进乱了，使用set paste设置一下：
```
set paste
```

## 退出粘贴模式
进入vim
```
set nopaste
```

## vim中清空内容
1. 按<kbd>ggdG`</kbd>
2. 命令模式下：
```
:%d
```

export HTTP_PROXY=http://127.0.0.1:1081; export HTTPS_PROXY=http://127.0.0.1:1081; export ALL_PROXY=socks5://127.0.0.1:1080