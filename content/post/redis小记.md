+++
title='Redis小记'
tags=['redis']
categories=['redis']
date="2018-09-06T11:21:39+08:00"
toc=true
url="redis-comment.html"
+++

Redis使用小记
<!--more-->

## 下载，解压、编译
```
wget http://download.redis.io/releases/redis-4.0.11.tar.gz
tar xzf redis-4.0.11.tar.gz
cd redis-4.0.11
make
```

## 安装
```
make PREFIX=/usr/local/redis install
```

> 因为编译安装在/usr/local/redis下，所以下面操作都是在目录/usr/local/redis/bin下完成   
> cd /usr/local/redis/bin
## 配置/etc/redis.conf

```
bind 192.168.2.188 #支持局域网访问
daemonize yes #后台运行
```
## 启动

```
./redis-server /etc/redis.conf
```

## 连接
```
./redis-cli -h 192.168.2.188
```

## 不安装，直接在线体验
[点此访问 web Redis https://try.redis.io](https://try.redis.io/)

## 常用操作
> Redis 有5个基本数据结构:字符串（string）、散列（hash）、列表（list）、集合（set）和有序集合（sorted set）

1. string 操作
> string 表示的是一个可变的字节数组，我们初始化字符串的内容、可以拿到字符串的长度，可以获取string的子串，可以覆盖string的子串内容，可以追加子串.
* 初始化字符串 提供【变量名称】和【变量内容】
```
set msg "hello world"
```
* 查看字符串内容
```
get msg
```
* 获取字符串长度 提供【变量名称】
```
strlen msg
```
* 获取子串 提供【变量名称】以及开始和结束位置 [start,end]
```
getrange msg 5 10
```
* 覆盖子串 提供【变量名称】以及开始位置和目标子串
```
setrange msg 5 " china"
```
* 追加子串 
```
append msg ", nihao."
```
* 批量操作
```
set name1 zhansan
set name2 lisi
```

1. 批量读
    ```
    mget name1 name2
    ```
2. 批量写
    ```
    mset name1 val1 name2 val2 name3 val3
    ```

* 设置过期时间和 set 命令扩展
```
expire name1 5       #5s 后过期
setex name1 5 val1   #5s 后过期，等价于set + expire
setnx name1 code     #如果name1不存在，就执行set创建
```
* 字符串的寿命
```
ttl name1            #返回正数表示过期时间，返回-2表示变量不存在，-1表示没有设置过期时间
```

* 对整数进行加减
> 自增的范围是signed long的最小，最大值【-9223372036854775808 ~ 9223372036854775807】，超过这个值，redis会报错
```
set age 30
incr age      # 加1
incrby age 5  # 加5
incrby age -5 # 减5
decr age      # 减1
decrby age 5  # 减5
```
* 主动删除
```
del msg
```
* 使用php API 操作 
https://github.com/phpredis/phpredis  
```
$redis = new Redis();
$redis->connect('192.168.2.188','6379',1);
$redis->set('msg','hello world');
$redis->get('msg');
$redis->delete('msg');
```

2.LIST 列表常用操作
> Redis 的列表结构常用来做异步队列使用。将需要延后处理的任务结构体序列化成字符串塞进 Redis 的列表，另一个线程从这个列表中轮询数据进行处理。
* 头元素和尾元素
> 头元素指的是列表左端/前端第一个元素，尾元素指的是列表右端/后端第一个元素。
* 空列表
> 指不包含任何元素的列表，Redis将不存在的key也视为空列表。

* 右边进左边出：队列 （先进先出）
```
rpush books python java php golang   # 添加元素
llen books                           # 队列元素长度
lpop books                           # 最先加入的python出来
lpop books
lpop books
lpop books 
```

* 右边进右边出：栈 （先进后出）
```
rpush books python php js css java golang  # 添加元素
rpop books                                 # 最后添加的golang出来
```

* 使用php API 操作
```
$redis->delete('language');
$redis->lPush('language','java');
$redis->lPush('language','php');
$redis->lPush('language','linux');
```

* RPUSH 将一个或多个值 value 插入到列表 key 的表尾(最右边)。
```
rpush language iOS #成功后返回list长度
```
* 使用php API 操作
```
$redis->rPush('language','iOS');
```

* RPUSHX
> RPUSHX key value   
> 当key存在并且是一个列表时，将值value插入到列表key的表尾。
```
rpushx language ruby
```
* 使用php API 操作
```
$redis->rPushX('language','ruby');
```

* LRANGE
> LRANGE key start stop 返回列表key中指定区间内的元素，区间以偏移量start和stop指定。   
> 0 表示第一个元素，1表示第二个元素，-1表示最后一个元素，-2表示倒数第二个元素，依此类推
```
LRANGE language 0 -1 返回所有
```
* 使用php API 操作
```
$arr = $redis->lRange('language', 0, -1);  # 若有值，返回数组
```

* LINDEX
> LINDEX key index
> 返回列表key中，下标为index的元素，0表示第一个，1表示第二个，-1表示最后一个，-2表示倒数第二个，依此类推...   
> 如果key不为list，返回错误

```
lindex language -1
```
* 使用php API 操作
```
$s = $redis->lget('language',1); 或 $s = $redis->lindex('language',1);
echo $s;
```
* LPOP
> 移除并返回列表 key 的头元素。
```
lpop language
```
* 使用php API 操作
```
$redis->lPop('language');
```
* RPOP
> RPOP key  
> 移除并返回列表key的尾元素  
```
rpop language
```
* 使用php API 操作
```
$redis->rPop('language');
```


* LLEN
> LLEN key  
> 返回列表key的长度，如果key不存在，返回0，如果key不是列表类型，返回一个错误。
```
llen language
```
* 使用php API 操作
```
$len = $redis->lSize('language');
或
$len = $redis->lLen('language');
```



* SADD 无序集合
> SADD key member [member ...]    
> 将一个或多个member元素加入到集合key当中，已经存在于集合的member元素将被忽略。    
> 假如key不存在，则创建一个只包含member元素作为成员的集合。当key不是集合类型时，返回一个错误。在Redis2.4版本以前，SADD只接受单个member值。

```
sadd nosql "Redis" "MongoDB" "Neo4j"
```
使用smembers查看刚设置的值
```
smembers nosql
```
* ZADD 有序集合
> 将一个或多个member元素及其score值加入到有序集key当中。  
> 如果某个member已经是有序集合的成员，那么更新这个member的score的值，并通过重新插入这个member元素，来保证该member在正确的位置上。    
> score值可以是整数值或双精度浮点数。   
> 如果key不存在，则创建一个空的有序集并执行ZADD操作。当key存在但不是有序集类型时，返回一个错误。
```
ZADD page_rank 9 baidu.com 8 bing.com
```

## 设置密码
```
CONFIG SET requirepass password # 密码设置为mypassword
```
下次再redis-cli -h 192.168.2.188操作时，就会提示
```
(error) NOAUTH Authentication required.
```
除非先使用auth mypassword解锁

## 清空密码：
```
CONFIG SET requirepass ''
```


