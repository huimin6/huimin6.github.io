# Dump

1.通过java -version查看虚拟机版本

2.通过jps查看java进程id, 或者ps -ef | grep java查看java进程的id
```
jps
ps -ef | grep java
```

3.通过jstack制作线程的dump信息, 可以用来查看线程的阻塞或者死锁等问题
```
jstack 进程id >> 文件名
```

Linux环境下制作线程dump
```
kill -quit 进程id
kill -3 进程id
```

4.通过下面的命令制作堆dump信息
```
jmap -histo:live 进程id
```
注意:谨慎使用jmap, jmap命令执行会直接触发一次fullGC
