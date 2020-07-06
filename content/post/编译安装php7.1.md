+++
title='编译安装php7'
tags=['php']
categories=['php']
date="2018-05-07T18:10:51+08:00"
toc=true
url="install_php7.html"
+++

记录一次在centos下编译安装php7
<!--more-->

## 下载安装php
* 下载
```
wget http://cn2.php.net/get/php-7.1.17.tar.gz/from/this/mirror -O php-7.1.17.tar.gz
```
* 解压
```
tar zxvf php-7.1.17.tar.gz
cd php-7.1.17
```
* 安装依赖项
```
yum -y install bzip2-devel
yum -y install curl-devel

wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-6.repo

yum install libmcrypt-devel
yum install readline-devel

```
* 编译
```
./configure --prefix=/mnt/soft/php7 \
--with-config-file-path=/mnt/soft/php7/etc \
--with-config-file-scan-dir=/mnt/soft/php7/etc/php.d \
--enable-inline-optimization \
--disable-debug \
--disable-rpath \
--enable-shared \
--enable-opcache \
--enable-fpm \
--with-fpm-user=www \
--with-fpm-group=www \
--with-mysqli=mysqlnd \
--with-jpeg-dir \
--with-png-dir \
--with-freetype-dir \
--with-gd \
--with-pdo-mysql=mysqlnd \
--with-gettext \
--enable-mbstring \
--with-iconv \
--with-mhash \
--with-mcrypt \
--with-openssl \
--enable-bcmath \
--enable-soap \
--enable-gd-native-ttf \
--with-libxml-dir \
--enable-pcntl \
--enable-shmop \
--enable-sysvmsg \
--enable-sysvsem \
--enable-sysvshm \
--enable-sockets \
--with-curl \
--with-zlib \
--enable-zip \
--with-bz2 \
--with-readline \
--without-sqlite3 \
--without-pdo-sqlite \
--with-pear
```
* 安装php7
```
make && make install
```

#### PHP配置
0. 建立软链接
```
ln -s /mnt/soft/php7/bin/php /usr/local/sbin/php
```
1. 将源文件中配置文件拷贝到安装目录下，即/mnt/soft/php7/etc
```
cp /mnt/src/php-7.1.17/php.ini-production /mnt/soft/php7/etc/php.ini
```
2. php.ini 设置
```
vim /mnt/soft/php7/etc.php.ini

date.timezone = Asia/Shanghai
memory_limit = 256M
session.name = PHP7SESSID
```

## php.fpm配置
1. 拷贝php-fpm.conf
```
cp /mnt/soft/php7/etc/php-fpm.conf.default /mnt/soft/php7/etc/php-fpm.conf
cd /mnt/soft/php7/etc/php-fpm.d
cp www.conf.default admin.conf
```
2. 配置admin.conf
```
vim admin.conf
listen = 127.0.0.1:9071
```
3.拷贝php-fpm启动脚本到系统目录
```
cp /mnt/src/php-7.1.17/sapi/fpm/init.d.php-fpm /etc/init.d/php7-fpm
chmod +x /etc/init.d/php7-fpm
```
4.启动php-fpm
```
/etc/init.d/php7-fpm start
```

## 使用composer
* 下载安装
```
cd /tmp
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === '544e09ee996cdf60ece3804abc52599c22b1f40f4323403c44d44fdfdd586475ca9813a858088ffbc1f233e9b180f061') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"

mv composer.phar /usr/local/sbin/composer 

```
* 设置全局中国镜像
> 注：下面的镜像已经不维护，最新请使用阿里云镜像：<a href='https://developer.aliyun.com/composer' target='_blank'>点此访问：https://developer.aliyun.com/composer</a>
```
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```

 
