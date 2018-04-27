centos7查看防火墙状态：firewall-cmd --state

1. 关闭firewall：
systemctl stop firewalld.service  停止firewall

systemctl disable firewalld.service  禁止firewall开机启动
