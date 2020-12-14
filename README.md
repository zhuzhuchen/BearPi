# BearPi虚拟机环境配置(个人配置过程分享)


## 虚拟机配置篇

#### 百度下载vmware安装

#### 下载[系统镜像](https://mirrors.tuna.tsinghua.edu.cn/ubuntu-releases/20.04.1/ubuntu-20.04.1-live-server-amd64.iso)并安装

我选用的是ubuntu 20.04 server版本,放在后台运行没有图形界面省资源,带图形界面的可以

系统安装最后记得有个选项开启ssh要记得开(带图形界面的没有这个选项),忘了也没关系后面进系统可以再安装启用

#### 连接虚拟机,点击虚拟机菜单栏(虚拟机-SSH-连接到SSH),会弹出命令窗口输入用户名和密码即可连接
#### 如果系统安装时忘了启用ssh可以执行以下命令

~~~ bash
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
~~~

系统最好升级一下,我没升级也可以用

~~~ bash
# 参考[清华软件源帮助](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/)改一下软件源,先备份一下 
sudo mv /etc/apt/sources.list /etc/apt/sources.list.bak

# 配置软件源
sudo nano /etc/apt/sources.list
# 添加以下内容

#####################################################
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
#####################################################


# 升级系统
sudo apt update
sudo apt upgrade
~~~



#### 参考小熊派环境搭建篇[BearPi-HM_Nano开发搭建环境](https://gitee.com/bearpi/bearpi-hm_nano/blob/master/applications/BearPi/BearPi-HM_Nano/docs/quick-start/BearPi-HM_Nano%E5%BC%80%E5%8F%91%E6%90%AD%E5%BB%BA%E7%8E%AF%E5%A2%83.md)其中有些步骤可以省略,我列出我的步骤

~~~ bash
# 写在前面,ubuntu 20.04的默认python版本满足需求,不用再安装了,也不用去下载源码编译,直接按以下操作进行就行了

# 链接python3为python
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
sudo pip3 install ecdsa
# 安装scons,这里小熊派教程让下载scons,其实pip就可以安装,apt也可以安装(版本有些老),我这里用pip安装,你也可以按照小熊派的教程下载二进制包进行配置
sudo pip3 install scons

~~~

#### 安装gn,ninja,gcc_riscv32

1. [下载gn工具](http://tools.harmonyos.com/mirrors/gn/1523/linux/gn.1523.tar)。
2. [下载ninja工具](http://tools.harmonyos.com/mirrors/ninja/1.9.0/linux/ninja.1.9.0.tar)。
3. [下载gcc_riscv32工具](http://tools.harmonyos.com/mirrors/gcc_riscv32/7.3.0/linux/gcc_riscv32-linux-7.3.0.tar.gz)。
4. 解压以上文件
~~~ bash
tar xvpf gn.1523.tar
tar xvpf ninja.1.9.0.tar
tar xvpf gcc_riscv32-linux-7.3.0.tar.gz
# 移动到opt目录下
sudo mv gn ninja gcc_riscv32 /opt/
#设置环境变量,我设置的是全局环境变量,一定不能输入错误的信息
sudo nano /etc/environment
# 在最后双引号前面加上:/opt/gcc_riscv32/bin:/opt/gn:/opt/ninja:/opt/node/bin
# 保存内容按ctrl+x
# 最终内容如下
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/opt/gcc_riscv32/bin:/opt/gn:/opt/ninja"

~~~

#### 安装配置samba

~~~ bash
sudo apt install samba
sudo systemctl enable samba
sudo nano /etc/samba/smb.conf
# 在文件最后添加以下内容,ctrl+x保存文件
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

#### 在windows上映射网络地址

右键点击网络,映射网络驱动器,在文件夹填写"\\你的虚拟机ip地址\HarmonyOS_Code",按照提示输入你输入的samba用户名和密码



#### 最后一件事,源码获取:

~~~ bash
git clone https://gitee.com/bearpi/bearpi-hm_nano -b master
~~~


#### 漏了一个nodejs,不过我刚开始没安装貌似也可以,我是强迫症,软件源安装npm会依赖python2.7,所以我直接用的nodejs的二进制包(吐槽一下,python2都停止维护了,还在依赖老旧软件)
1. 下载[nodejs二进制包](https://nodejs.org/dist/v14.15.1/node-v14.15.1-linux-x64.tar.xz)
2. 解压
~~~ bash
tar xvpf node-v14.15.1-linux-x64.tar.xz
~~~
3. 移动到opt目录下
~~~ bash
sudo mv node-v14.15.1-linux-x64 /opt/node
~~~
4. 添加全局环境变量
~~~ bash
sudo nano /etc/environment
# 在最后双引号前面加上:/opt/node/bin
# 保存内容按ctrl+x
# 最终内容如下
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/opt/gcc_riscv32/bin:/opt/gn:/opt/ninja:/opt/node/bin"
~~~

## windowd配置篇

#### 下载[VSCode](https://mirrors.tuna.tsinghua.edu.cn/github-release/VSCodium/vscodium/LatestRelease/)并安装,我选用的是社区版的vscodium,功能基本一致
#### 下载[HUAWEI DevEco Device Tool](https://device.harmonyos.com/cn/ide)并安装
devicetool-device-1.0.0.1.zip得到DevEcoDeviceTool-1.0.1.vsix,打开vscodium或vscode按ctrl+shift+x打开扩展管理,将vsix文件拖放到这里完成安装
在windows文件管理器打开C:\Users\你的用户名\.vscode-oss\extensions\huawei.devecodevicetool-1.0.1\deveco\tools找到hiburn.exe,复制到你能找到的位置,这是烧录程序

#### 下载[MobaXterm](https://mobaxterm.mobatek.net/)
按照[小熊派开发环境搭建配置mobaxterm](https://gitee.com/bearpi/bearpi-hm_nano/blob/master/applications/BearPi/BearPi-HM_Nano/docs/quick-start/BearPi-HM_Nano%E5%BC%80%E5%8F%91%E6%90%AD%E5%BB%BA%E7%8E%AF%E5%A2%83.md)连接虚拟机,当然只用虚拟机提供的ssh也可以

#### 编译
ssh或者mobaxterm连接到虚拟机后cd命令切换到你的源码目录,执行python build.py BearPi-HM_Nano开始编译

#### 烧录
用第二步得到的hiburn.exe烧录程序




