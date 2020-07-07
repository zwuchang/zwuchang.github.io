+++
title='Linux_bash基础'
tags=['centos7','bash']
categories=['bash']
date="2020-07-07T13:46:44+08:00"
toc=true
url='centos_bash.html'
+++

shell的简单历史：第一个流行的shell是由Steven Bourne发展出来的，为了纪念他所以就称为Bourne shell ，或直接简称为sh ！而后来另一个广为流传的shell是由柏克莱大学的Bill Joy设计依附于BSD版的Unix系统中的shell ，这个shell的语法有点类似C语言，所以才得名为C shell ，简称为csh ！由于在学术界Sun主机势力相当的庞大，而Sun主要是BSD的分支之一，所以C shell也是另一个很重要而且流传很广的shell之一。

由于Linux是用C语言编写的，很多程序员使用C来开发软件，因此C shell相对的就很热门了。
目前我们的Linux (以CentOS 7.x 为例) 有多少我们可以使用的shells 呢？你可以检查一下/etc/shells 这个档案，至少就有底下这几个可以用的shells ：

* /bin/sh (已经被/bin/bash 所取代)  
* /bin/bash (就是Linux 预设的shell)  
* /bin/tcsh (整合C Shell ，提供更多的功能) 
* /bin/csh (已经被/bin/tcsh 所取代) 
<!--more-->

Linux默认使用的就是bash，所以下面就来简单介绍下bash的使用：

## 历史命令 history
在终端操作过的命令，默认会将最近1000个执行都会记录下来，存储在~/.bash_history，不过要注意的是，当你成功退出后，才会记录到这个文件里。当前操作的命令，是存在于内存中的。
* 查询指定条数
```
history 10
```
* 清除当前所有历史记录
```
history -c
```
* 将当前历史记录写入文件
```
history -w
```
* 执行第n号命令
```
!3   ---> 执行history显示的第3条命令
```
* 执行上条命令
```
!!   ---> 即!3
```
* 执行最近以!h开头的命令
```
!h  ---> 若最近执行过history或其它以h开头的命令，将会被执行
```
## 命令和文件补全[tab]
输入部分命令和文件名后，按<kbd>Tab</kbd>键，系统会尝试去匹配剩下的内容

## 命令别名[alias]
用简单命令取代一长串命令，或习惯问题，想沿用其它系统上的命令
```
alias cls='clear'   
alias lm='ls -al'
unalias lm          ---> 取消lm别名
```
## 变量设置与读取
```
name='zhangsan'
echo ${name}
```

## PATH累加
在原有PATH下加上/root/go/bin
```
PATH=$PATH:/root/go/bin
```
## 命令查找顺序
1. 以相对/绝对路径执行命令，例如『 /bin/ls 』或『 ./ls 』；
2. 由alias 找到该指令来执行；
3. 由bash 内建的(builtin) 指令来执行；
4. 透过$PATH 这个变量的顺序搜寻到的第一个指令来执行。

## 脚本读取顺序
终端下设置的别名，变量都是暂时的，退出后就没有了，需要一直生效，就需要写入到预设的脚本文件中，如~/.bashrc
![login_shell读取流程](/images/2020/07/centos7_bashrc_1.gif)

## 脚本立即生效(source)
* source ~/.bashrc  ---> 立即执行~/.bashrc，无需退出再登录
* . ~/.bashrc ---> 与上同效