+++
title='缓解mac空间不足，为Docker Desktop腾出空间'
author='yang'
tags=['mac']
categories=['docker','mac']
date="2024-07-05T15:46:39+08:00"
toc=true
+++

当前是 2024 年了，我还在用着 256G 的 Macbook Pro，还运行着 Docker Desktop，可想而知这硬盘空间是有多着急。
没有找到直接更改存放数据目录的方法，只能曲线救国，利用软链接，将默认目录指向移动硬盘上的目录，这样就可以将 Docker Desktop 的数据存放在移动硬盘上，从而释放 Macbook Pro 的硬盘空间。

<!--more-->

## 0. 备份默认数据目录
1. 占击最上菜单栏，停止 Docker Desktop
2. 保险起见，操作之前当然要先备份了
```
mv ~/Library/Containers/com.docker.docker/Data ~/Library/Containers/com.docker.docker/Data.bak
```
## 1. 创建目录
> /Volumes/T7 是我移动硬盘的名称
```
mkdir -p /Volumes/T7/com.docker.docker/Data
cp -R ~/Library/Containers/com.docker.docker/Data.bak /Volumes/T7/com.docker.docker/Data
```

## 2. 指向新目录
```
ln -s /Volumes/T7/com.docker.docker/Data ~/Library/Containers/com.docker.docker/Data
```

## 3. 验证是否生效
最后，启动 Docker Desktop，查看是否正常
一切没问题后，为了释放之前占用的空间，可以删除原备份目录~/Library/Containers/com.docker.docker/Data