Linux系统安装Ruby2.5.1最新版

下载：curl -O -L https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.1.tar.gz

解压：tar -zxvf ruby-2.5.1.tar.gz

进入目录：cd ruby-2.5.1/

编译：./configure --prefix=/usr/local/ruby-2.5.1

安装： make

安装： mke install

创建集群：./redis-trib.rb create --replicas 1 192.168.88.131:7001 192.168.88.131:7002 192.168.88.131:7003 192.168.88.131:7004 192.168.88.131:7005  192.168.88.131:7006

连接集群：./redis-cli -h 127.0.0.1 -p 6379

