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
## Redis介绍

Redis是一种基于键值对（key-value）的NoSQL数据库，是一种内存数据库。Redis中的数据是存储在内存中的，一般来说存储在内存中的数据在断电的时候便会消失，

但是Redis提供了两种数据库持久化方案。由于数据是存储在内存中，所以数据的读取速度可以达到每秒万级。所以大型的网站会利用Redis做缓存，来降低对数据库的访

问量，提高网站的性能。

## Redis中的五种数据类型

字符串（string）

列表（list）

哈希（hash）

集合（set）

有序集合（zset）


