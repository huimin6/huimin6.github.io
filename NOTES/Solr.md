## solr单机版

### solr单机版的tomcat开机自启配置

(1)打开/etc/rc.d/rc.local文件，在其中添加如下内容：
```
export JAVA_HOME=/usr/local/java/jdk1.8.0_172  
export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.  
export PATH=$PATH:$JAVA_HOME/bin  
export CATALINA_HOME=/usr/local/solr/tomcat
```
