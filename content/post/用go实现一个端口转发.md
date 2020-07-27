+++
title='用go实现一个端口转发'
tags=['go','端口转发']
categories=['go']
date="2020-07-27T18:23:15+08:00"
toc=true
url='go-forward.html'
+++

我的使用场景：主机是windows10，使用自带的Hyper-V安装了CentOS7，虚拟交换机用的默认的，每次win10重启后，会给虚拟主机分配新IP。怎么用我熟悉的命令来监听本机IP，需要ssh连接虚拟机的时候，直接root@本机IP就可以了？  
1、使用之前介绍的 <a href='/windows_portproxy.html' target='_blank'>《windows端口转发》</a>也可以完成，就是那串命令太长了，总记不住，我给虚拟机设定了固定IP后，重启win10后，不知为啥转发不生效了，要重新设置一次。  
2、有没更简短的命令实现同样的效果呢？不用关心虚拟机IP是多少，想到了go，结合脚本自动汇报IP：
<!--more-->

![显示截图](/images/2020/07/hyper_network.png)

## 用Go起2个服务

```golang
package main

import (
	"fmt"
	"io"
	"net"
	"net/http"
	"os"
	"time"
)

var targetIP string

const waitSecond = 120 // 等待时间
var (
	tranNum int // 总转发数
	httpNum int // 总接收http数
)

func main()  {
	go httpServer()
	proxyStart(22, 22)
}

// 每次从光标开始处输出
func beginEcho(str string) {
	fmt.Printf("\r") // 光标回到开始处
	fmt.Printf("[%s] %s ", time.Now().Format("2006-01-02 15:04:05.000"), str)
}

func echo(str string) {
	fmt.Printf("[%s] %s \n", time.Now().Format("2006-01-02 15:04:05.000"), str)
}

func httpServer() {
	mux := http.NewServeMux()
	mux.HandleFunc("/register", func(w http.ResponseWriter, r *http.Request) {
		ip := r.FormValue("ip")
		result := "success"
		address := net.ParseIP(ip)
		if address != nil {
			httpNum += 1
			targetIP = address.String()
			result = fmt.Sprintf("(%d)收到请求，注册目标IP为:%s", httpNum, targetIP)
		} else {
			result = ip + " IP地址格式不正确！"
		}
		beginEcho(result)
		w.Write([]byte(result + "\n"))
	})

	echo("http listen:8888")
	err := http.ListenAndServe(":8888", mux)
	if err != nil {
		fmt.Printf("http listen fail :%v \n", err)
		os.Exit(2)
	}
}

func proxyStart(fromPort, toPort int) {
	proxyaddr := fmt.Sprintf(":%d", fromPort)
	proxyListener, err := net.Listen("tcp", proxyaddr)
	if err != nil {
		fmt.Printf("Unable to listen on: %s, error: %s\n\n", proxyaddr, err.Error())
		os.Exit(1)
	}
	echo("tcp listen" + proxyaddr)
	defer proxyListener.Close()

	for {
		proxyConn, err := proxyListener.Accept()
		if err != nil {
			fmt.Printf("Unable to accept a request, error: %s\n", err.Error())
			continue
		}

		buffer := make([]byte, 1024)
		n, err := proxyConn.Read(buffer)
		if err != nil {
			if err != io.EOF {
				fmt.Printf("Unable to read from input, error: %s\n", err.Error())
			}
			continue
		}

		if len(targetIP) == 0 {
			echo("收到请求，等待目标主机注册:8888/register?ip=")
			for i := 1; i <= waitSecond; i++ {
				beginEcho(fmt.Sprintf("wait %d second ... ", i))
				time.Sleep(time.Second)
				if len(targetIP) > 0 {
					echo("目录IP注册成功")
					break
				}
			}

		}

		// 过了指定时间，没收到请求，关闭此次连接
		if len(targetIP) == 0 {
			proxyConn.Close()
			continue
		}
		targetAddr := fmt.Sprintf("%s:%d", targetIP, toPort)
		targetConn, err := net.Dial("tcp", targetAddr)
		if err != nil {
			fmt.Printf("Unable to connect to: %s, error: %s\n\n", targetAddr, err.Error())
			proxyConn.Close()
			continue
		}

		tranNum += 1
		beginEcho(fmt.Sprintf("(%d)接收到[%s]请求，转发到 %s", tranNum, proxyConn.RemoteAddr(), targetAddr))

		n, err = targetConn.Write(buffer[:n])
		if err != nil {
			fmt.Printf("Unable to write to output, error: %s\n", err.Error())
			proxyConn.Close()
			targetConn.Close()
			continue
		}

		go io.Copy(proxyConn, targetConn)
		go io.Copy(targetConn, proxyConn)
	}
}

```

## 虚拟机定时汇报IP
1. 获取IP脚本
```
[root@localhost ~]# cat /root/bin/registerIP.sh
#/bin/bash
IP=`/usr/sbin/ip a|grep -w 'inet'|grep 'global'|sed 's/^.*inet //g'|sed 's/\/[0-9][0-9].*$//g'`

#echo "------ ${IP} -------- \n"
url="http://192.168.9.166:8888/register?ip=${IP}"

curl "${url}" 2> /dev/null 1> /dev/null
#echo "url: ------ ${url} ---- \n"
exit 0

```

2. 定时汇报
以下内容加到/etc/crontab里，每分钟汇报一次
```bash
1/* * * * * root sh /root/bin/registerIP.sh
```


