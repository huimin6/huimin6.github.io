<!-- MarkdownTOC -->

- [Redis](#redis)
- [Redis安装](#redis安装)
- [Redis介绍](#redis介绍)
- [Redis中的五种数据类型](#redis中的五种数据类型)
    + [字符串string](#字符串string)

<!-- /MarkdownTOC -->

# Redis

# Redis安装
一.安装Ruby2.5.1最新版

下载：curl -O -L https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.1.tar.gz

解压：tar -zxvf ruby-2.5.1.tar.gz

进入目录：cd ruby-2.5.1/

编译：./configure --prefix=/usr/local/ruby-2.5.1

安装： make

安装： mke install

创建集群：./redis-trib.rb create --replicas 1 192.168.88.131:7001 192.168.88.131:7002 192.168.88.131:7003 192.168.88.131:7004 192.168.88.131:7005  192.168.88.131:7006

连接集群：./redis-cli -h 127.0.0.1 -p 6379 -c


二.设置redis开机自动启动：

1.赋予脚本可执行权限（/usr/local/start-all.sh是你的脚本路径）
chmod +x /usr/local/start-all.sh

2.输入命令：vim /etc/rc.d/rc.local ，在末尾增加如下内容: /usr/local/start-all.sh

3.在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限: chmod +x /etc/rc.d/rc.local

设置Redis开机自启动

创建start-all.sh文件，文件内容如下：

```html
cd redis01
./redis-server redis.conf
cd ..
cd redis02
./redis-server redis.conf
cd ..
cd redis03
./redis-server redis.conf
cd ..
cd redis04
./redis-server redis.conf
cd ..
cd redis05
./redis-server redis.conf
cd ..
cd redis06
./redis-server redis.conf
cd ..
```
# Redis介绍

Redis是一种基于键值对（key-value）的NoSQL数据库，是一种内存数据库。Redis中的数据是存储在内存中的，一般来说存储在内存中的数据在断电的时候便会消失，
但是Redis提供了两种数据库持久化方案。由于数据是存储在内存中，所以数据的读取速度可以达到每秒万级。所以大型的网站会利用Redis做缓存，来降低对数据库的访
问量，提高网站的性能。

# Redis中的五种数据类型

## 字符串string

(key, value)

字符串类型是Redis中最基础的数据类型，其中value的类型可以是字符串（JSON, XML也可以）、数字、二进制（图片、视频等）类型，但是value最大不能超过512MB。

典型的应用场景：

1.缓存：使用Redis作为缓存层，MySQL作为数据存储层，降低数据库的访问压力

2.共享session：将session全部存储在Redis中集中管理

3.限速（验证码获取次数）

4.计数（视频点击次数）

列表（list）

哈希（hash）

集合（set）

有序集合（zset）

# Redis压力测试

命令1：

100个并发 100000个请求

```
redis-benchmark -h 127.0.0.1 -p 6379 -c 100 -n 100000
```
测试时会选取redis支持的命令，例如set和get等进行测试，默认请求是3字节

命令2：

使用-d命令指定请求的字节为100进行测试

```
redis-benchmark -h 127.0.0.1 -p 6379 -q -d 100
```

命令3：

指定测试的命令为set和lpush

```
redis-benchmark -t set,lpush -q -n 100000
```
命令4：

执行命令（'set', 'foo', 'bar'）进行测试

```
redis-benchmark -n 100000 -q script load "redis.call('set','foo','bar')"
```
