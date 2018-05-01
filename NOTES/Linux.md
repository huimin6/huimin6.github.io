<!-- GFM-TOC -->
* [Linux](#Linux)
    * [1.防火墙firewall](#1防火墙firewall)
    * [2.重启与关机](#2重启与关机)
    * [3.软件安装](#3软件安装)
<!-- GFM-TOC -->

# Linux

## 1.防火墙firewall

(1) centos7查看防火墙状态：firewall-cmd --state

(2) systemctl stop firewalld.service  停止firewall

(3) systemctl disable firewalld.service  禁止firewall开机启动

## 2.重启与关机

(1) centos重启命令：

　　1.reboot
  
　　2.shutdown -r now 立刻重启(root用户使用)
  
　　3.shutdown -r 10 过10分钟自动重启(root用户使用)
  
　　4.shutdown -r 20:35 在时间为20:35时候重启(root用户使用)
  
　　如果是通过shutdown命令设置重启的话，可以用shutdown -c命令取消重启

(2) centos关机命令：

　　1.halt 立刻关机
  
　　2.poweroff 立刻关机
  
　　3.shutdown -h now 立刻关机(root用户使用)
  
　　4.shutdown -h 10 10分钟后自动关机
  
　　如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启
 
