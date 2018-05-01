<!-- GFM-TOC -->
* [Linux](#Linux)
    * [1.防火墙firewall](#1防火墙firewall)
    * [2.重启与关机](#2重启与关机)
    * [3.vi/vim命令](#3vi和vim命令)
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
  
## 3.vi和vim命令

1.复制

  复制一行则：yy 

  复制三行：3yy，即从当前光标+下两行

  复制当前光标所在的位置到行尾：y$ 

  复制当前光标所在的位置到行首：y^

2.剪切

  剪切一行：dd 

  前切三行：3dd,即从当前行+下两行被剪切了。

  剪切当前行光标所在的位置到行尾：d$ 

  剪切当前行光标所在的位置到行首：d^

3.粘贴

用v选中文本之后可以按y进行复制，如果按d就表示剪切，之后按p进行粘贴

4.撤销与恢复

'u' : 撤销上一个编辑操作

'ctrl + r' : 恢复，即回退前一个命令 

'U' : 行撤销，撤销所有在前一个编辑行上的操作

5.屏幕翻页 

Ctrl+u: 向上翻半屏 

Ctrl+f: 向上翻一屏 

Ctrl+d: 向下翻半屏 

Ctrl＋b: 向下翻一屏

6.移动光标指令 

移动光标普遍使用的是方向键，考虑兼容问题，vi定义太多的方向指令，下面只是一小小部分（常用的几个）： 

space: 光标右移一个字符 

Backspace: 光标左移一个字符 

Enter: 光标下移一行 

nG: 光标移至第n行首 

n+: 光标下移n行 

n-: 光标上移n行 

n[Math Processing Error]: 光标移至当前行尾

7.插入删除指令 

i：在当前光标前插入，光标后文本向后移 

a：从当前光标后插入，光标后文本后移

I：在光标所在行首插入（第一个非空白字符前） 

A：从光标所在行末插入 

o: 在光标所在行下面新增一行（并进入输入模式）

O: 在光标所在行上方新增一行（并进入输入模式）

x: 删除光标所在字符，等同于[Delete]功能键 

X: 删除光标前字符，相当与[Backspace] 

dd: 删除光标所在的行 

r: 修改光标所在字符 

R: 替换当前字符及其后的字符，直到按 [ESC] 

s: 从当前光标位置处开始，以输入的文本替代指定数目的字符 

S: 删除指定数目的行，并以所输入文本代替之 

do: 删至行首 

d$: 删至行尾

6.退出 

退出输入模式，先按一下[ESC]键（有时要多按两下），然后执行： 

:w! 

:w ——保存当前文件 

:wq —— 存盘退出(与指令 :x 功能相同) 

:q —— 直接退出，如已修改会提示是否保存 

:q! ——不保存直接退出
 
