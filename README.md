# BearPi环境配置

## 安装vmware和ubuntu虚拟机

百度下载vmware安装

下载镜像:https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04.1/ubuntu-20.04.1-live-server-amd64.iso 

我选用的是ubuntu 20.04 server版本,放在后台运行没有图形界面省资源,带图形界面的可以

系统安装最后记得有个选项开启ssh要记得开(带图形界面的没有这个选项),忘了也没关系后面进系统可以再安装启用

系统最好升级一下,我没升级也可以用

~~~ bash
sudo apt update
sudo apt upgrade
~~~



## 配置系统环境

如果系统安装时忘了启用ssh可以执行以下命令

~~~ bash
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
~~~

连接虚拟机,点击虚拟机菜单栏(虚拟机-SSH-连接到SSH),会弹出命令窗口输入用户名和密码即可连接

参考小熊派环境搭建篇[BearPi-HM_Nano开发搭建环境](https://gitee.com/bearpi/bearpi-hm_nano/blob/master/applications/BearPi/BearPi-HM_Nano/docs/quick-start/BearPi-HM_Nano%E5%BC%80%E5%8F%91%E6%90%AD%E5%BB%BA%E7%8E%AF%E5%A2%83.md)其中有些步骤可以省略,我列出我的步骤

~~~ bash
# 连接python3位python
cd /usr/bin
ln -s python3 python
# 安装pip,setuptools
sudo apt-get install python3-setuptools python3-pip -y
# 安装kconfiglib
sudo pip3 install kconfiglib
# 安装pycryptodome
sudo pip3 install pycryptodome
#安装six
sudo pip3 install six
#安装ecdsa
sudo pip3 install scons
# 安装scons
sudo pip3 install scons

~~~

安装gn

1. 打开Linux编译服务器终端。
2. [下载gn工具](http://tools.harmonyos.com/mirrors/gn/1523/linux/gn.1523.tar)。
3. 解压gn安装包至~/gn路径下："tar -xvf gn.1523.tar -C ~/"。
4. 设置环境变量："vim ~/.bashrc", 新增："export PATH=~/gn:$PATH"。
5. 生效环境变量："source ~/.bashrc"。

安装ninja

1. 打开Linux编译服务器终端
2. [下载ninja工具](http://tools.harmonyos.com/mirrors/ninja/1.9.0/linux/ninja.1.9.0.tar)。
3. 解压ninja安装包至~/ninja路径下："tar -xvf ninja.1.9.0.tar -C ~/"。
4. 设置环境变量："vim ~/.bashrc", 新增："export PATH=~/ninja:$PATH"。
5. 生效环境变量："source ~/.bashrc"。

安装gcc_riscv32（WLAN模组类编译工具链）

1. 打开Linux编译服务器终端。
2. [下载gcc_riscv32工具](http://tools.harmonyos.com/mirrors/gcc_riscv32/7.3.0/linux/gcc_riscv32-linux-7.3.0.tar.gz)。
3. 解压gcc_riscv32安装包至/opt/gcc_riscv32路径下："tar -xvf gcc_riscv32-linux-7.3.0.tar.gz -C ~/"。
4. 设置环境变量："vim ~/.bashrc"，新增："export PATH=~/gcc_riscv32/bin:$PATH"。
5. 生效环境变量："source ~/.bashrc"。
6. Shell命令行中输入“riscv32-unknown-elf-gcc -v”，如果能正确显示编译器版本号，表明编译器安装成功。

安装配置samba

~~~ bash
sudo apt install samba
sudo systemctl enable samba
sudo nano /etc/samba/smb.conf
# 在最后添加以下内容
[HarmonyOS_Code]
path = 你要放源码的目录
available = yes
valid users = 你要设置的samba用户名
read only = no
browsable = yes
public =yes
writable = yes

# 设置samba登陆密码
sudo smbpasswd -a 你要设置的samba用户名
# 启用samba服务
sudo systemctl start samba


~~~

在windows上映射网络地址



最后一件事,源码获取:

~~~ bash
git clone https://gitee.com/bearpi/bearpi-hm_nano -b master
~~~





