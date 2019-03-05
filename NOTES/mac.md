# mac的使用

## mac安装oh-my-zsh
1.克隆仓库，在终端执行命令

git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh

2.如果你已存在~/.zshrc文件，则备份现有的~/.zshrc文件，在终端执行如下命令

cp ~/.zshrc ~/.zshrc.orig

3.创建一个新的zsh配置文件

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

4.改变默认的Shell

chsh -s /bin/zsh

5.修改oh-my-zsh的themes

终端执行

vi ~/.zshrc

6.修改文件中的ZSH_THEME="miloshadzic"中的名字
