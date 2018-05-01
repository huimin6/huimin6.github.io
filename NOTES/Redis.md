设置Redis开机自启动

创建start-all.sh文件，文件内容如下：
'''
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
'''
