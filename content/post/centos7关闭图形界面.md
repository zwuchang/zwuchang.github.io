---
title: "Centos7关闭图形界面"
date: 2020-07-03T18:44:53+08:00
tags: ["linux"]
categories:  ["linux"]
url: "cenots7-set-default.html"
---

利用win10自带的虚拟机管理工具Hyper-V安装好CentOS7，把图形界面也给装上了，内存吃得厉害。赶紧把它关掉
<!--more-->

* graphical.target 图形+文字模式
* multi-user.target 纯文字模式
  
## 查看  
查看默认的target，执行：
```
systemctl get-default
```

## 设置
开机以命令模式启动，执行：
```
systemctl set-default multi-user.target
```

开机以图形界面启动，执行：
```
systemctl set-default graphical.target
```

重启后就会进入设置的模式中
```
reboot
```

## 实时切换
不需要重启关闭图形界面：
```
systemctl isolate multi-user.target
```

相应地，不重启进入图形界面：
```
 systemctl isolate graphical.target
```