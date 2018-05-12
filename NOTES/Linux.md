<!-- MarkdownTOC -->

- [Linux](#linux)
    + [1.防火墙firewall](#1防火墙firewall)
    + [2.重启与关机](#2重启与关机)
    + [3.vi和vim命令](#3vi和vim命令)
        * [1.复制](#1复制)
        * [2.剪切](#2剪切)
        * [3.粘贴](#3粘贴)
        * [4.撤销与恢复](#4撤销与恢复)
        * [5.屏幕翻页](#5屏幕翻页)
        * [6.移动光标指令](#6移动光标指令)
        * [7.插入删除指令](#7插入删除指令)
        * [8.退出](#8退出)
    + [安装JDK](#安装jdk)

<!-- /MarkdownTOC -->

# Linux

## 1.防火墙firewall

centos7系统

|命令|功能|
|-|-|
| firewall-cmd --state | 查看防火墙状态 |
| systemctl stop firewalld.service | 停止firewall |
| systemctl disable firewalld.service | 禁止firewall开机启动 |

## 2.重启与关机

(1) centos重启命令：

|命令|功能|
|-|-|
| reboot | 重启 |
| shutdown -r now | 立刻重启(root用户使用) |
| shutdown -r 10 | 过10分钟自动重启(root用户使用) |
| shutdown -r 20:35 | 在时间为20:35时候重启(root用户使用) |

如果是通过shutdown命令设置重启的话，可以用shutdown -c命令取消重启

(2) centos关机命令：

|命令|功能|
|-|-|
| halt | 立刻关机 |
| poweroff | 立刻关机 |
| shutdown -h now | 立刻关机(root用户使用) |
| shutdown -h 10 | 10分钟后自动关机 |

如果是通过shutdown命令设置关机的话，可以用shutdown -c命令取消重启
  
## 3.vi和vim命令

### 1.复制

|命令|功能|
|:-:|-|
| yy | 复制一行 |
| 3yy | 复制三行(从当前光标+下两行) |
| y$ | 复制当前光标所在的位置到行尾 |
| y^ | 复制当前光标所在的位置到行首 |

### 2.剪切

|命令|功能|
|:-:|-|
| dd | 剪切一行 |
| 3dd | 前切三行 |
| d$ | 剪切当前行光标所在的位置到行尾 |
| d^ | 剪切当前行光标所在的位置到行首 |

### 3.粘贴

用v选中文本之后可以按y进行复制，如果按d就表示剪切，之后按p进行粘贴

### 4.撤销与恢复

|命令|功能|
|:-:|-|
| u | 撤销上一个编辑操作 |
| ctrl + r | 恢复，即回退前一个命令 |
| U | 行撤销，撤销所有在前一个编辑行上的操作 |

### 5.屏幕翻页 

|命令|功能|
|-|-|
| Ctrl+u  | 向上翻半屏 |
| Ctrl+f | 向上翻一屏 |
| Ctrl+d | 向下翻半屏 |
| Ctrl+b | 向下翻一屏 |

### 6.移动光标指令 

移动光标普遍使用的是方向键，考虑兼容问题，vi定义太多的方向指令，下面只是一小小部分（常用的几个）： 

|命令|功能|
|:-:|-|
| space | 光标右移一个字符 |
| Backspace | 光标左移一个字符 |
| Enter | 光标下移一行 |
| nG | 光标移至第n行首 |
| n+ | 光标下移n行 | 
| n- | 光标上移n行 |
| G | 光标移动到文本最后一行 |

### 7.插入删除指令 

|命令|功能|
|:-:|-|
| i | 在当前光标前插入，光标后文本向后移 | 
| a | 从当前光标后插入，光标后文本后移 |
| I | 在光标所在行首插入（第一个非空白字符前）| 
| A | 从光标所在行末插入 | 
| o | 在光标所在行下面新增一行（并进入输入模式）|
|O | 在光标所在行上方新增一行（并进入输入模式）
|x | 删除光标所在字符，等同于[Delete]功能键 |
|X | 删除光标前字符，相当与[Backspace] |
|dd | 删除光标所在的行 |
|r | 修改光标所在字符 |
|R | 替换当前字符及其后的字符，直到按 [ESC] |
|s | 从当前光标位置处开始，以输入的文本替代指定数目的字符 |
|S | 删除指定数目的行，并以所输入文本代替之 |
|do | 删至行首 |
|d$ | 删至行尾|

### 8.退出 

退出输入模式，先按一下[ESC]键（有时要多按两下），然后执行： 

| 命令 | 作用 |
| :-: | - |
| :w! | 执行强制存盘操作|
| :w | 执行存盘操作|
| :wq | 存盘退出(与指令 :x 功能相同)|
| :q | 直接退出，如已修改会提示是否保存|
| :q! | 不保存直接退出|
## 安装JDK

(1)查询jdk版本：java -version

'''
openjdk version "1.8.0_161"
OpenJDK Runtime Environment (build 1.8.0_161-b14)
OpenJDK 64-Bit Server VM (build 25.161-b14, mixed mode)
'''

上面的内容说明Linux系统已经自动安装openJDK，我们将其卸载掉然后安装SUN公司的JDK,如果显示的是

'''
-bash: /usr/bin/java: No such file or directory
'''
就说明，系统中没有安装JDK需要我们自己安装

(2)卸载已经安装的JDK

'''
rpm -e --nodeps java-1.7.0-openjdk
rpm -e --nodeps java-1.8.0-openjdk
rpm -e --nodeps java-1.7.0-openjdk-headless 
rpm -e --nodeps java-1.8.0-openjdk-headless 
'''
(3)下载SUN公司的JDK并安装

1)进入到/usr/local目录，并创建java目录：

'''
cd /usr/local
mkdir java
'''

2)解压jdk-8u172-linux-x64.tar.gz文件，并复制到java目录中：

'''
tar -zxf jdk-8u172-linux-x64.tar.gz
cp -r jdk1.8.0_172/ /usr/local/java
'''

3)打开/etc/profile文件，配置环境变量

'''
 vim  /etc/profile
 在文件末尾加入如下内容：
 export JAVA_HOME=/usr/local/java/jdk1.8.0_172
 export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
 export PATH=$PATH:$JAVA_HOME/bin
'''
4)修改完成之后退出，输入如下命令使修改生效：

'''
source /etc/profile
'''