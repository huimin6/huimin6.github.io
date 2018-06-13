<!-- MarkdownTOC -->

- [Redis](#redis)
- [Redis安装](#redis安装)
- [Redis介绍](#redis介绍)
- [Redis中的五种数据类型](#redis中的五种数据类型)
    + [字符串string](#字符串string)

<!-- /MarkdownTOC -->

# Redis

# Redis安装
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
