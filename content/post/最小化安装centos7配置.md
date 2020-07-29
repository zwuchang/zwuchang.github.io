+++
title='最小化安装centos7配置'
tags=['centos7']
categories=['centos7']
date="2020-07-09T14:38:20+08:00"
toc=true
url="mini_install_centos7_config.html"
+++

最小化安装centos后，一些熟悉的管理工具需要单独去安装
<!--more-->

## IP配置
编辑 /etc/sysconfig/network-scripts/ifcfg-eth0 
```
ONBOOT=yes              #开机启动

IPADDR=192.168.1.22     #IP地址
PREFIXO=24              #设置子网掩码
GATEWAY=192.168.1.1     #设置网关
DNS1=114.114.114.114    #DNS
BOOTPROTO=static        #固定IP

```
## 更新系统
这里只会更新或升级已安装的软件，不会安装任何新的软件：
```
yum update && yum upgrade
```

## 安装软件
* 安装net-tools (提供ifconfig)，vim，cifs-utils(挂载windows共享目录用)
```
yum install -y ifconfig vim cifs-utils wget git
yum groups install "Development Tools"
```


## 安装go
* 下载go安装文件
```
curl https://golang.org/dl/go1.14.4.linux-amd64.tar.gz
```

* 解析至/usr/local目录
```
tar -C /usr/local -vxzf go1.14.4.linux-amd64.tar.gz
```
* 配置PATH和代理，编辑~/.bashrc，最后添加以下内容
```
# 当go命令不存在时，添加到path
type go > /dev/null 2>&1
if [ $? -ne 0 ] ;then
    export GOPATH=$HOME/go
    export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin
    go env -w GO111MODULE=on
    go env -w GOPROXY=https://goproxy.io,direct
fi

```
* 使刚添加的配置生效
```
source ~/.bashrc
```

* 测试安装是否成功，写一个测试文件hello.go
```
package main

import "fmt"

func main() {
	fmt.Printf("hello， world\n")
}
```
* 然后运行下 `go run hello.go`，如果有如下输出，说明成功了：
```
hello， world
```