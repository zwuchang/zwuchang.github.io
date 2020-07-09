+++
title='Centos7挂载win10共享目录'
tags=['win','centos7']
categories=['win','centos7']
date="2020-07-08T11:18:14+08:00"
toc=true
url="centos7_mount_win10_share.html"
+++

记录一下在我windows10通过Hyper-V安装的Centos7挂载windows10共享目录的设置
<!--more-->

## 共享设置

打开需要共享的目录，右击“属性”--点击“共享”--选择需要共享的用户后，点“共享”，windows目录共享设置就完成了。
![共享设置截图](/images/2020/07/win10_share1.png)
![共享设置截图](/images/2020/07/win10_share2.png)
![共享设置截图](/images/2020/07/win10_share3.png)

## Centos7挂载
> centos 需安装 `yum install -y cifs-utils`
把pc换成你windows共享时设置的用户名，ip换成你windows的IP地址：
```
mkdir /mnt/win10share
mount -t cifs -o username=pc //192.168.9.166/share /mnt/win10share
```

执行以上命令
![设置截图](/images/2020/07/win10_share4.png)
回车输入密码后，有可能提示：
![设置截图](/images/2020/07/win10_share5.png)

打开“本地安全策略”---找到“网络访问: 本地帐户的共享和安全模型”，将它设为如下："经典 - 对本地用户进行身份验证，不改变其本来身份”。
![设置截图](/images/2020/07/win10_share6.png)
![设置截图](/images/2020/07/win10_share7.png)

再回到Centos7上输入`mount -t cifs -o username=pc //192.168.9.166/share /mnt/win10share`，输入密码，回车后没任何提示，说明成功了。
接下来可以在windows对这个目录操作或在centos对这个目录操作，都是实时同步了。