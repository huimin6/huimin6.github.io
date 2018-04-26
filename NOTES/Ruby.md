Linux系统安装Ruby2.5.1最新版

下载：curl -O -L https://cache.ruby-lang.org/pub/ruby/2.5/ruby-2.5.1.tar.gz

解压：tar -zxvf ruby-2.5.1.tar.gz

进入目录：cd ruby-2.5.1/

编译：./configure --prefix=/usr/local/ruby-2.5.1

安装： make

安装： mke install

创建集群：./redis-trib.rb create --replicas 1 192.168.88.131:7001 192.168.88.131:7002 192.168.88.131:7003 192.168.88.131:7004 192.168.88.131:7005  192.168.88.131:7006

连接集群：./redis-cli -h 127.0.0.1 -p 6379 -c


设置redis开机自动启动：

1.赋予脚本可执行权限（/usr/local/start-all.sh是你的脚本路径）
chmod +x /usr/local/start-all.sh

2.输入命令：vim /etc/rc.d/rc.local ，在末尾增加如下内容: /usr/local/start-all.sh

3.在centos7中，/etc/rc.d/rc.local的权限被降低了，所以需要执行如下命令赋予其可执行权限: chmod +x /etc/rc.d/rc.local

