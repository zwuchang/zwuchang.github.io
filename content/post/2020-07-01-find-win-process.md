+++
title="解除win端口占用"
tags=["win"]
categories=["win"]
date="2020-07-01T15:00:00+08:00"
toc=true
+++

> 查找某个端口被哪个程序占用并结束它

## 查找特定端口
```
netstat -ano | findstr "6379"
```

## 根据PID找到对应进程
```
tasklist | findstr "pid num"
```

## 结束进程
```
taskkill /f /t /im 进程名.exe
```

