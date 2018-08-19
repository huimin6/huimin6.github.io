<!-- MarkdownTOC -->

- [Redis](#redis)
- [Redis安装](#redis安装)
- [Redis介绍](#redis介绍)
    + [Redis中的五种数据类型](#redis中的五种数据类型)
- [Redis集群中 leader 的选举算法](#redis集群中-leader-的选举算法)
- [Redis压力测试](#redis压力测试)
- [Redis 中的事务](#redis-中的事务)
- [Redis缓存更新的策略](#redis缓存更新的策略)

<!-- /MarkdownTOC -->

# Redis

# Redis安装
1.安装Ruby2.5.1最新版

下载：curl -O -L https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.1.tar.gz

解压：tar -zxvf ruby-2.5.1.tar.gz

进入目录：cd ruby-2.5.1/

编译：./configure --prefix=/usr/local/ruby-2.5.1

安装： make

安装： mke install

创建集群：./redis-trib.rb create --replicas 1 192.168.88.131:7001 192.168.88.131:7002 192.168.88.131:7003 192.168.88.131:7004 192.168.88.131:7005  192.168.88.131:7006

连接集群：./redis-cli -h 127.0.0.1 -p 6379 -c


2.设置redis开机自动启动：

(1)创建start-all.sh文件，文件内容如下：

```
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
(2)赋予脚本可执行权限（/usr/local/start-all.sh是你的脚本路径）
chmod +x /usr/local/start-all.sh

(3)输入命令：vim /etc/rc.d/rc.local ，在末尾增加如下内容: /usr/local/start-all.sh

(4)在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限: chmod +x /etc/rc.d/rc.local

# Redis介绍

Redis是一种基于键值对（key-value）的NoSQL数据库，是一种内存数据库。Redis中的数据是存储在内存中的，一般来说存储在内存中的数据在断电的时候便会消失，
但是Redis提供了两种数据库持久化方案。由于数据是存储在内存中，所以数据的读取速度可以达到每秒万级。所以大型的网站会利用Redis做缓存，来降低对数据库的访
问量，提高网站的性能。

## Redis中的五种数据类型

1.字符串string

(key, value)

字符串类型是Redis中最基础的数据类型，其中value的类型可以是字符串（JSON, XML也可以）、数字、二进制（图片、视频等）类型，但是value最大不能超过512MB。

典型的应用场景：

a.缓存：使用Redis作为缓存层，MySQL作为数据存储层，降低数据库的访问压力

b.共享session：将session全部存储在Redis中集中管理

c.限速（验证码获取次数）

d.计数（视频点击次数）

2.列表（list）

3.哈希（hash）

4.集合（set）

5.有序集合（zset）

# Redis集群中 leader 的选举算法

参考博客：[Raft 协议实战之 Redis Sentinel 的选举 Leader 源码解析](http://weizijun.cn/2015/04/30/Raft%E5%8D%8F%E8%AE%AE%E5%AE%9E%E6%88%98%E4%B9%8BRedis%20Sentinel%E7%9A%84%E9%80%89%E4%B8%BELeader%E6%BA%90%E7%A0%81%E8%A7%A3%E6%9E%90/)

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

# Redis 中的事务

Redis 中的事务和我们所理解的 MySQL 中的事务不太一样，它是通过 MULTI 来标记一个事务的开始，然后将要执行的一组命令依次写入，最后通过 EXEC 命令执行上述事务中的命令。除非出现语法错误，否则这组命令都会被执行，即使执行过程中某个命令失败，也不会回滚，下一个命令仍然会被执行。
| 命令 | 功能 |
|:-:|:-|
| MULTI | 开启事务 |
| EXEC | 执行事务中的命令 |
| DISCARD | 取消事务，放弃执行事务中的所有命令 |
| WATCH | 监控某个 (或多个) 键，如果在事务执行之前键对应的值发生变化，事务将被打断 |
| UNWATCH | 取消对某个 (或多个) 键的监控 |

# Redis缓存更新的策略

先更新数据库，成功后，让缓存失效，虽然这样也会存在并发问题，如脏读，但是发生的概率很小

虽然写 db 成功后，失效 cache 也会有并发问题：更新和读并发 

(1)查询 cache，miss，读 db

(2)写 db，失效 cache

(3)写 chache

导致 cache 中脏数据，但是概率极低，并且一般 db 中写时间长于读时间，并且写会锁表，读需要在写前进入，并且要晚于写操作更新缓存，所以发生概率极低。

解决方法是 2PC 或是 Paxos 协议，代价较大。

所以我们采用的方式是：

写数据只写 db

更新数据先更新 db，再失效 cache

读数据，先读 cache，未命中读 db，写入 cache

RabbitMQ 的面试问题

1.