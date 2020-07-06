+++
title='Supervisor安装及使用'
tags=[]
categories=['supervisord']
date="2018-08-06T16:59:07+08:00"
toc=true
+++

Supervisor安装及使用
<!--more-->

> Supervisor (<a href='http://supervisord.org' target='_blank' >点此访问http://supervisord.org</a>) 是一个用 Python 写的进程管理工具，可以很方便的用来启动、重启、关闭进程（不仅仅是 Python 进程）。除了对单个进程的控制，还可以同时启动、关闭多个进程，比如很不幸的服务器出问题导致所有应用程序都被杀死，此时可以用 supervisor 同时启动所有应用程序而不是一个一个地敲命令启动。

* supervisord为服务端，supervisorctl为客户端。

## 安装supervisord
* CentOS
```
yum install supervisor
```

* MacOS安装
```
brew install supervisor
```

## 启动 supervisord
* CentOS

```
supervisord -c /etc/supervisord.conf 
或
systemctl start supervisord
```

* MacOS
  
```
brew services start superivsor
```
1. 启动supervisor
```
brew services start superivsor
```
2. 前台运行supervisor
```
supervisord -c /usr/local/etc/supervisord.ini
```

## 管理natapp
1. 下载natapp
```
mkdir /usr/local/natapp
cd /usr/local/natapp
wget http://download.natapp.cn/assets/downloads/clients/2_2_2/natapp_linux_amd64_2_2_2.zip
unzip natapp_linux_amd64_2_2_2.zip
```

2. 建立natapp配置文件
```
mkdir /etc/supervisord.d
vim /etc/supervisord.d/natapp.ini 

如 下载的 natapp在 /usr/local/natapp/natapp 
[program:natapp]
command                 = /usr/local/natapp/natapp -authtoken=xxxxxx -log=stdout
directory               = /usr/local/natapp
process_name            = %(program_name)s_%(process_num)s
numprocs                = 1   ;注意这里只能为1 
autostart               = true
autorestart             = true

;以下为日志,按实际情况修改
stdout_logfile          = /var/log/supervisor/natapp_stdout.log
stdout_logfile_maxbytes = 10MB
stderr_logfile          = /var/log/supervisor/natapp_error.log
stderr_logfile_maxbytes = 10MB
```

## 配置 supervisord.conf
```
若不存在supervisord.conf可利用echo_supervisord_conf /etc/supervisord.conf 生成配置文件
在 /etc/supervisord.conf 添加如下配置
[include]
files = supervisord.d/*.ini
```


## 启动 supervisorctl客户端
```
supervisorctl
```
就可以看到刚配置的natapp是否启动正常，可以对它进行重启，关闭等操作。
如果更改了supervisord配置文件，在supervisorctl下，可以使用
```
reload 重新加载supervisord
```

## 将supervisord加入开机启动
```
systemctl enable supervisord.service
```

## 注意
要让supervisord能管理nginx,php-fpm等启动，关闭，须配置nginx,php-fpm不能以deamon方式运行：  
nginx:
```
daemon off; #nginx不能以daemon模式启动
user nobody;
 
```

php-fpm:
```
daemonize = no;
```

redis 
```
daemonize no
supervised upstart
```

## Ubuntu安装
1.查看版本信息
```
apt-cache show supervisor
```
2.安装
```
apt-get install supervisor
```

### 使用supervisor来管理Laravel队列
## 管理horizon
```
[program:horizon]
process_name=%(program_name)s
command=php /home/forge/app.com/artisan horizon
autostart=true
autorestart=true
user=forge
redirect_stderr=true
stdout_logfile=/home/forge/app.com/horizon.log
```

## 更改了队列
```
php artisan horizon:terminate
```
