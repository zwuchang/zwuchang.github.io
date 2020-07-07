+++
title = "Linux命令小记"
date = "2020-07-03T16:06:06+08:00"
tags = ["linux"]
categories = ["linux"]
toc = true
+++

一些linux命令
<!--more-->
## 运行程序
1. 命令行下运行
```
./filename  运行filename命令
ctrl+c 退出当前运行的命令
```

2. 后台运行
```
nohup command > out.file 2>&1&  后台运行command,正常输出和错误输出都写入out.file
ps -ef | grep 关键字 | awk '{print $2}' | xargs kill 9 退出
```

3. 服务方式运行
```
systemctl enable xxx  设置开机启动
systemctl start xxx   启动
systemctl stop xxx    关闭
```

4. 快速删除终端下命令

<kbd>Ctrl</kbd>+<kbd>u</kbd> 删除光标开始到最左面的所有命令
<kbd>Ctrl</kbd>+<kbd>k</kbd> 删除光标开始到最右面的所有命令

<kbd>Ctrl</kbd>+<kbd>a</kbd> 移动光标到命令最左侧
<kbd>Ctrl</kbd>+<kbd>e</kbd> 移动光标到命令最右侧



## 安装软件
1. 下载安装包方式   
* centos rpm
```
安装  rpm -i jdk-xxx_linux-x64_bin.rpm
查找  rpm -qa | grep jdk
列表  rpm -qa | more
```
* ubuntu dpkg
```
安装 dpkg -i jdk-xxx_linux-x64_bin.deb
查找 dpkg -l | grep jdk
列表 dpkg -l | more
```
2. 软件管家方式
* centos yum
```
搜索 yum search jdk
安装 yum install java-11-openjdk.x86_64
删除 yum erase java-11-openjdk.x86_64
配置文件 /etc/yum.repos.d/CentOS-Base.repo
```
* ubuntu apt-get
```
搜索 apt-cache search jdk
安装 apt-get install openjdk-9-jdk
删除 apt-get purge openjdk-9-jdk
配置文件 /etc/apt/sources.list
```

