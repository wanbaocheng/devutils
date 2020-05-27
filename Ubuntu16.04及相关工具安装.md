## 安装 Ubuntu系统
先用Ubuntu系统中的启动盘制作工具制作一个U盘安装盘，然后正常安装即可（比如按F10进入BIOS）。如果安装了多个系统，可能需要指定[引导程序](https://blog.csdn.net/u012986684/article/details/79508694)：
```
sudo update-grub
sudo grub-install /dev/sd*
```
其中 sd* 改为对应的系统所在的分区。
如果需要把[Ubuntu /home下中文目录修改成英文](https://blog.csdn.net/fei2636/article/details/79202102)，可以
```
export LANG=en_US
xdg-user-dirs-gtk-update
```
可能遇到在图形界面用Delete键删除文件或文件夹到回收站功能不好使的情况，这可能是回收站文件夹（在/home/用户名/.local/share/下）的权限问题，用命令chmod修改即可。

启动Ubuntu16系统后发现桌面丢失（只有背景，没有图标）的情形，做法如下：
首先按Ctrl+Alt+F1进入命令行模式（输入用户名和密码），然后
```shell script
$ sudo apt update
$ sudo service lightdm stop
$ sudo apt install --reinstall ubuntu-desktop
$ sudo service lightdm start
```

系统进入登录界面，但键盘和鼠标没有反应：

- 进入grub然后选择高级模式进入recover mode
- 选择 enable network
- 进入root shell，输入
```shell script
$ sudo apt install xserver-xorg-input-all
```
- 重启
```shell script
$ reboot
```

# Nvidia显卡驱动安装
### 1. 下载NVIDIA官方驱动
查看当前电脑的显卡型号
```
lshw -numeric -C display
```
然后到[官网下载](https://www.nvidia.com/Download/index.aspx?lang=cn)，选择对应型号和版本。

### 2. 禁用nouveau
打开编辑配置文件：
```
sudo gedit /etc/modprobe.d/blacklist.conf
```
在最后一行添加：
```
blacklist nouveau
```
这一条的含义是禁用nouveau第三方驱动，之后也不需要改回来。由于nouveau是构建在内核中的，所以要执行下面命令生效:
```
sudo update-initramfs -u
```
删除原有的NVIDIA驱动程序（如果有的话）
```
sudo apt-get purge nvidia*
```
重启
```
reboot
```

### 3. 安装驱动
进入虚拟终端
```
Ctrl + Alt + F1
```
输入用户名和密码进入命令行。此时可以查看nouveau有没有运行:
```
lsmod | grep nouveau  # 没输出代表禁用生效
```
停止可视化桌面
```
sudo service lightdm stop
```
然后
```
sudo ./Nvidia(Tab补全)
```
接着启动可视化桌面
```
sudo service lightdm start
```
最后退出虚拟终端，进入可视化桌面
```
Ctrl + Alt + F7
```

### 4. 验证
```
nvidia-smi
```
参考：https://blog.csdn.net/wf19930209/article/details/81877822
# 终端
在终端中根据当前输入的部分命令字符按向上或向下键以进行向前或向后的历史命令过滤查找，输入
```
sudo gedit /etc/inputrc
```
进入编辑界面，查找 history-search-backward 和 history-search-forward 所在的行修改为
```
"\e[A": history-search-backward
"\e[B": history-search-forward
```
存盘即可。

调整history记录数目，打开~/.bashrc，在末尾输入
```
export HISTSIZE=20000
```
即可。
# 浏览器
### google chrome
用百度搜索
```
chrome 下载
```
进入官网下载 deb安装包（比如 google-chrome-stable_current_amd64.deb），然后
```
sudo dpkg -i google-chrome-stable_current_amd64.deb
```
即可。
# 开发工具
## 切换gcc，g++版本
参见 https://www.jianshu.com/p/f66eed3a3a25,
https://www.cnblogs.com/weinyzhou/p/4983306.html
## snap
[安装过程](https://docs.snapcraft.io/installing-snap-on-ubuntu)
```
sudo apt update
sudo apt install snapd
```
然后重启或者登出你的系统。
## Tilda
```
sudo apt update
sudo apt install tilda
```

## vim
安装 vim8.x：
```
sudo add-apt-repository ppa:jonathonf/vim
sudo apt update
sudo apt install vim
```
如果想卸载，可以
```
sudo apt remove vim
sudo add-apt-repository --remove ppa:jonathonf/vim
```
还可以配置一个[python的IDE](http://codingpy.com/article/vim-and-python-match-in-heaven/)。

## SpaceVim
Install spacevim (Linux && macOS, for windows),它不会删除你原有配置
```
curl -sLf https://spacevim.org/install.sh | bash
```
卸载并回滚到原vim
```
curl -sLf https://spacevim.org/install.sh | bash -s -- --uninstall
```
参见 https://blog.csdn.net/weixin_34260991/article/details/87041702
## 安装python
- 下载

到官网下载[Linux版](https://www.python.org/downloads/source/)

- 安装一些依赖模块
```shell script
$ sudo apt-get install build-essential checkinstall
$ sudo apt-get install libreadline-gplv2-dev libncursesw5-dev libssl-dev \
    libsqlite3-dev tk-dev libgdbm-dev libc6-dev libbz2-dev liblzma-dev
```
- 编译

进入python的根目录，然后
```shell script
$ sudo ./configure --enable-optimizations --enable-shared CFLAGS=-fPIC
```
其中加上--enable-shared和-fPIC，可以将python的动态链接库编译出来，否则只有python3.xm.a这样的文件。
默认安装目录是/usr/local, 例如可以通过添加--prefix=/opt/python改变为/opt/python。

- 安装
```shell script
$ sudo make altinstall
```

- 添加lib到路径上

方法一：
```shell script
$ vim ~/.bashrc
```
在文件末尾添加一行
```shell script
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```
方法二：

把/usr/local/lib下面的.so文件拷贝到/usr/lib目录下


注：参考[安装过程](https://www.jianshu.com/p/6059f7fc2cd0)，可能需要改变python的[符号链接](https://www.cnblogs.com/travellife/p/4064183.html)，最后安装[虚拟环境virtualenv](https://www.cnblogs.com/zh605929205/p/7705192.html)，也可采用python标准库的[venv](https://zhuanlan.zhihu.com/p/81568689)。

## anaconda3
到官网下载 [Linux版](https://www.anaconda.com/distribution/)，[Anaconda installer archive](https://repo.anaconda.com/archive/)，[anaconda与python版本对应关系](https://blog.csdn.net/yuejisuo1948/article/details/81043823)，比如文件名为Anaconda3-XXXX.XX-Linux-x86_64.sh，拷贝到 /opt 目录下，然后在命令行执行
```
sudo ./Anaconda3-XXXX.XX-Linux-x86_64.sh
```
一直 y 即可
注意：在使用 conda 安装软件包的过程中可能会遇到 EnvironmentNotWritableError 或者 EnvironmentError 错误，使用
```
sudo chown -R usr foldername    # usr：你登录的用户名，foldername：anaconda的绝对路径
```
为了提升pip的下载速度，可以在使用pip的时候加参数-i https://pypi.tuna.tsinghua.edu.cn/simple
例如：pip install -i https://pypi.tuna.tsinghua.edu.cn/simple torch，这样就会从清华这边的镜像去安装torch库。或者一劳永逸的解决：
修改 ~/.pip/pip.conf (没有就创建一个文件夹及文件。文件夹要加“.”，表示是隐藏文件夹)
内容如下：
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host=mirrors.aliyun.com
```
参考：https://www.cnblogs.com/microman/p/6107879.html
下载 torch
```
pip install torch
```
下载 opencv
```
pip install opencv-python
```
## pycharm
到官网下载 [Linux 社区版](https://www.jetbrains.com/pycharm/download/#section=linux)，解压到 /opt 目录下即可。

## CLion
参见 https://blog.csdn.net/wuyiduer/article/details/80012391
##### 在工具条上添加Reload CMake Project 和 Reset Cache and Reload Project
- 鼠标指针放在工具条的空白处点击右键，选择 Customize Menus and Toolbars...
- 打开Main Toolbar，选择最后一项
- 选择对话框上部的 +，点击 Add Action...
- 依次展开 Main menu -> Tools -> CMake
- 按住Ctrl，选择 Reload CMake Project 和 Reset Cache and Reload Project, 点击底部OK按钮


## Visual Studio Code
到[官网](https://code.visualstudio.com/docs/setup/linux#_debian-and-ubuntu-based-distributions)按步骤执行即可，然后可[配置 cmake 的 C++ 项目模板](http://www.pianshen.com/article/8310115346/)。

## eclipse
参见 https://www.cnblogs.com/zyrblog/p/8548270.html

## vcpkg 
```
git clone https://github.com/Microsoft/vcpkg.git
cd vcpkg
./bootstrap-vcpkg.sh
```
注：[vcpkg包下载缓慢的解决办法](https://blog.csdn.net/qq_39690181/article/details/82910610)
## PCL
```
sudo add-apt-repository ppa:v-launchpad-jochen-sprickerhof-de/pcl
sudo apt-get update
sudo apt-get install libpcl-dev
```
或者到[官网](https://github.com/PointCloudLibrary/pcl/releases/tag/pcl-1.9.1) 下载PCL源码，然后编译。可参考[PCL1.8+Ubuntu16.04安装详解](https://blog.csdn.net/dantengc/article/details/78446600)

## [starUML](http://staruml.io/)
- 下载软件  
到[这里](http://staruml.io/download/releases/StarUML-3.2.1.AppImage)下载
- 解压缩  
```
$ chmod +x StarUML-3.0.1-x86_64.AppImage
$ ./StarUML-3.0.1-x86_64.AppImage --appimage-extract
```
得到“squashfs-root”文件夹
- 准备解压缩“app.asar”
  - 安装npm
  ```
  $ sudo apt install npm
  $ sudo npm install n -g
  $ sudo n stable
  $ sudo npm install -g asar
  ```
  注：这一步有可能会报错：“/usr/bin/env: node: 没有那个文件或目录”，可以
  ```
  $ sudo ln -s /usr/bin/nodejs /usr/bin/node
  ```
  - 进入“squashfs-root/resources/”，解压app.asar
  ```
  $ asar extract app.asar app
  ```
- 进入“app”目录修改源代码  
  - 编辑“./src/engine/license-manager.js”  
  找到checkLicenseValidity () 
  做如下修改：
  ```
  checkLicenseValidity () {
      this.validate().then(() => {
        setStatus(this, true)
      }, () => {
        // 原来的代码：
        // setStatus(this, false) 
        // UnregisteredDialog.showDialog()
   
        //修改后的代码
        setStatus(this, true)
      })
    }
  ```
  - 重新打包替换原来的app.asar  
  回到“squashfs-root/app/resources”，执行
  ```
  $ asar pack app app.asar
  ```
- 运行“squashfs-root/app/staruml”

## [ROS](https://www.ros.org/)
- [安装ROS](https://www.jianshu.com/p/6ae840a94e2f)
  - 备份原始数据
  ```shell script
  $ cd /etc/apt/
  $ sudo cp sources.list sources.list.bak
  $ sudo gedit sources.list
  ```
  - 添加源
  ```shell script
  $ sudo sh -c '. /etc/lsb-release && echo "deb https://mirrors.ustc.edu.cn/ros/ubuntu/ $DISTRIB_CODENAME main" > /etc/apt/sources.list.d/ros-latest.list'
  ```
  - 设置密钥
  ```shell script
  $ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key F42ED6FBAB17C654
  ```
  - 更新系统软件库
  ```
  $ sudo apt update
  ```
  注：可能ROS由于公钥更新导致用户该步报错，参见[解决办法](https://blog.csdn.net/asdli/article/details/91978069?depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1&utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-1)
  - 安装ROS全功能版
  ```
  $ sudo apt install ros-kinetic-desktop-full
  ```
  - 查看可使用的包
  ```
  $ apt-cache search ros-kinetic
  ```
  - 安装重要插件（可选）
  ```shell script
  $ sudo apt install python-rosinstall
  ```
- 初始化ROS
  - 初始化 rosdep
  ```
  $ sudo rosdep init
  $ rosdep update
  ```
  - 然后初始化环境变量（重要）
  ```
  $ echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
  $ source ~/.bashrc
  ```
- 测试ROS
```shell script
$ roscore
$ rviz  # 另启一个终端
```

- 与vcpkg配合(可选)

在
rosROOT/kinetic/share/catkin/cmake/toplevel.cmake中找到类似
```shell script
if(NOT DEFINED CMAKE_PREFIX_PATH)
```
这样的行然后进行修改如下：
```
```shell script
  if(NOT DEFINED CMAKE_PREFIX_PATH)
    if(NOT "$ENV{CMAKE_PREFIX_PATH}" STREQUAL "")
      if(NOT WIN32)
        string(REPLACE ":" ";" CMAKE_PREFIX_PATH $ENV{CMAKE_PREFIX_PATH})
      else()
        set(CMAKE_PREFIX_PATH $ENV{CMAKE_PREFIX_PATH})
      endif()
    endif()
  else()
    if(NOT "$ENV{CMAKE_PREFIX_PATH}" STREQUAL "")
      if(NOT WIN32)
        string(REPLACE ":" ";" CMAKE_PREFIX_PATH_TMP $ENV{CMAKE_PREFIX_PATH})
        list(APPEND CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH_TMP})
      else()
        set(CMAKE_PREFIX_PATH $ENV{CMAKE_PREFIX_PATH})
      endif()
    endif()
  endif()
```
编译时类似这样
```shell script
$ catkin_make -DCMAKE_TOOLCHAIN_FILE=vcpkgROOT/scripts/buildsystems/vcpkg.cmake
```

## [CoppeliaSim](https://www.coppeliarobotics.com/)
-  下载

进入https://www.coppeliarobotics.com/ubuntuVersions，选择对应的版本，比如CoppeliaSim Edu, Ubuntu 16.04

- 安装

解压到/opt/v-rep目录下即可

- 安装ROSInterface(便于与ROS的通信，可选)

拷贝compiledRosPlugins目录下的libsimExtROSInterface.so到根目录（其父目录）

- 启动
```shell script
$ source /opt/ros/kinetic/setup.bash
$ ./coppeliaSim.sh
```
检查出现如下信息
```shell script
Plugin 'ROSInterface': loading...
Plugin 'ROSInterface': load succeeded.
```
表明ROSInteface安装成功。 

- 使用示例时遇到的小问题

启动coppeliaSim，点击主菜单 File --> Open scene...，打开Loading scene...对话框，
选中文件rosInterfaceTopicPublisherAndSubscriber.ttt，点击 Open 按钮，
然后打开一个新终端，输入
```shell script
$ source /opt/ros/kinetic/setup.bash
$ rosrun image_view image_view image:=/image
```
此时屏幕输出如下出错信息
```shell script
qt.qpa.plugin: Could not find the Qt platform plugin "xcb" in ""
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.
ldd platforms/libqxcb.so
```
[原因分析](https://blog.csdn.net/lusanshui/article/details/84988017?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromBaidu-5)：

应用找不到libqxcb.so文件

解决办法：
```shell script
$ sudo ln -sf /usr/lib/x86_64-linux-gnu/qt5/plugins/platforms/libqxcb.so /usr/bin/libqxcb.so
```

## [ZeroBrane Studio](https://studio.zerobrane.com/)
- 下载

https://download.zerobrane.com/ZeroBraneStudioEduPack-1.90-linux.sh

- 安装
```shell script
$ ./ZeroBraneStudioEduPack-1.90-linux.sh
```
- 卸载
```shell script
$ zbstudio-uninstall
```
- 启动Studio
```shell script
$ zbstudio
```

- 选择lua解释器

主菜单 project --> Lua Interpreter --> Lua 5.3


## [CloudCompare](https://www.danielgm.net/cc/)
两种方式：
- 使用 snap  
   sudo snap install cloudcompare --edge
- 源码编译  
  到 [github](https://github.com/cloudcompare/cloudcompare) 或者[Alberto Mardegan](https://github.com/mardy/CloudCompare)上，按照上面的步骤安装即可。注意在安装的过程中可以提示缺少qt库等，可参考[该页](https://stackoverflow.com/questions/40688812/could-not-find-a-package-configuration-file-provided-by-qt5svg)。

## 安装nvidia版的docker
- 基础docker  
  根据[docker官网](https://docs.docker.com/install/linux/docker-ce/ubuntu/)所述步骤进行安装。
- 安装Nviida Container toolkit  
  根据[官网](https://github.com/NVIDIA/nvidia-docker)所述步骤安装。
- 为docker添加镜像源
```shell script
$ sudo vim /etc/docker/daemon.json # 如果没有就新建
```  
  在文件中输入
```json
{
    "registry-mirrors": ["https://cr.console.aliyun.com/"]
}
``` 
## nginx
```
$ sudo /usr/local/nginx/sbin/nginx  # 启动nginx
$ sudo /usr/local/nginx/sbin/nginx -s stop  # 关闭nginx
$ sudo vim /usr/local/nginx/conf/nginx.conf  # 编辑nginx配置
```
[防火墙和端口](https://blog.csdn.net/weixin_38750084/article/details/90387056)
以下假设IP地址是12.34.56.78
```shell script
$ sudo systemctl status firewalld   # 防火墙的状态
$ sudo firewall-cmd --list-ports    # 当前打开的端口，如果没有则无任何输出
$ sudo systemctl start firewalld    # 启动防火墙
$ sudo firewall-cmd --zone=public --add-port=80/tcp --permanent   # 开启80端口
$ sudo firewall-cmd --reload        # 重启防火墙，注意为了使新开启的端口生效必须执行该操作
$ ping IP地址                       # 是否能链接到IP地址对应的服务器
$ telnet IP地址 80                  # 测试IP地址对应的服务器的80端口是否开启
```

## ssh
在终端中远程登录服务器后，长时间没有交互会导致服务器断链，解决办法有两种：  
- 方法一（本地）  
```shell script
$ vim ~/.ssh/config
```
输入
```shell script
ServerAliveInterval 60
```
然后重新打开终端即可。

- 方法二（服务器）  
修改root 目录下的.bash_profile文件，加上
```shell script
export TMOUT=86400  # 以秒为单位
```
注意设置完成后，要退出ssh远程连接，再次登录后才可以生效，因为要再读取一次.bash_profile文件。

## tmux
- 安装  
```shell script
# Ubuntu 或 Debian
$ sudo apt-get install tmux

# CentOS 或 Fedora
$ sudo yum install tmux

# Mac
$ brew install tmux
```
- [tmux持久保存session](https://segmentfault.com/a/1190000015143625)  
  - 安装插件管理器TPM
  ```shell script
  # 把管理器文件安装到`~/.tmux/plugins/tpm`之下 此前这些目录是不存在的
  git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
  # 新建配置文件
  touch ~/.tmux.conf
  vim ~/.tmux.conf
  # 将下面内容复制到`~/.tmux.conf`
  # List of plugins
  set -g @plugin 'tmux-plugins/tpm'
  set -g @plugin 'tmux-plugins/tmux-sensible'
  # Other examples:
  # set -g @plugin 'github_username/plugin_name'
  # set -g @plugin 'git@github.com/user/plugin'
  # set -g @plugin 'git@bitbucket.com/user/plugin'
  # Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
  run '~/.tmux/plugins/tpm/tpm' 

  # 在tmux运行的时候，找到任意窗口输入下面这个完成安装管理器：
  tmux source ~/.tmux.conf
  ```
  - 安装tmux-resurrect插件  
  由于tmux不能持久保存session的特性，我们需要安装这个插件来将session的设置完全保存到本地，然后重启后也能够快速恢复窗口等设置的内容。
首先在~/.tmux.conf文件的List of plugins部分加入这句话：
  ```shell script
  set -g @plugin 'tmux-plugins/tmux-resurrect'
  ```
  保存好后，在tmux的任意窗口按ctrl+b及大写的I，即可完成下载安装。
  
- 基本使用方法  
  参见：  [Tmux 使用教程](https://www.ruanyifeng.com/blog/2019/10/tmux.html)
# 小工具
## ubuntu下分解大压缩文件或解压拆分后的文件
参考网址[https://blog.csdn.net/xunan003/article/details/79068648](https://blog.csdn.net/xunan003/article/details/79068648)

## 两文本文件内容比较软件
```shell script
$ sudo apt-get update
$ sudo apt-get install diffuse
```
 