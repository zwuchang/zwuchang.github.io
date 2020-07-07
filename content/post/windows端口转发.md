+++
title='Windows端口转发'
tags=['win']
categories=['win']
date="2020-07-07T11:15:36+08:00"
toc=true
url="windows_portproxy.html"
+++

通过windows自带工具netsh，实现任何传入到本地端口的TCP连接，都重定向到本地指定端口或本地可访问的远程端口。
<!--more-->

## 添加转发

* 例：将请求本地win10主机192.168.1.9:22的请求转发到虚拟机192.168.226.244:22中
> listenaddress – 等待连接的本地IP地址。
  listenport – 本地侦听TCP端口。
  connectaddress – 将传入连接重定向到本地或远程IP地址（或DNS名称）。
使用管理员运行cmd:
```bash
netsh interface portproxy add v4tov4 listenaddress=192.168.1.9 listenport=22 connectaddress=192.168.226.244 connectport=22
```

使用netstat查看本机22端口是否处于监听状态
```
netstat -ano | findstr :3340
 ```

 需要如果局域网其它主机通过192.168.1.9:22访问，请确保防火墙入站规则允许22端口

## 显示列表

显示系统转发规则列表
```
netsh interface portproxy show all
```
![显示截图](/images/2020/07/20200707113108showall.png)

## 删除转发
* 删除指定的端口转发规则
```
netsh interface portproxy delete v4tov4 listenport=22 listenaddress=192.168.1.9
```
![删除指定截图](/images/2020/07/20200707113108_del_one.png)
* 清空当前转发规则
```
netsh interface portproxy reset
```