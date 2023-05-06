sudo apt-get update  --更新软件源

sudo apt-get upgrade -更新已经安装的软件

##### ssh密钥登陆

A为本地主机(即用于控制其他主机的机器) ;
B为远程主机(即被控制的机器Server), 假如ip为192.168.254.128;
A和B的系统都是Linux

在A上运行命令:
\# ssh-keygen -t rsa (连续三次回车,即在本地生成了公钥和私钥,不设置密码)
\# ssh root@192.168.254.128 "mkdir .ssh" (需要输入密码)
\# scp ~/.ssh/id_rsa.pub root@192.168.254.128:.ssh/id_rsa.pub (需要输入密码)

在B上的命令:
\# touch /root/.ssh/authorized_keys (如果已经存在这个文件, 跳过这条)
\# cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys (将id_rsa.pub的内容追加到authorized_keys 中)

回到A机器:
\# ssh root@192.168.254.128 (不需要密码, 登录成功)

##### 升级某一cmake版本

$ wget http://www.cmake.org/files/v3.11/cmake-3.11.3.tar.gz
$ tar xzvf cmake-3.11.3.tar.gz
$ cd cmake-3.11.3
$ ./configure
$ make
$ sudo make install

