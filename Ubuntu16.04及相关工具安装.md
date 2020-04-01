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
参见 https://ywnz.com/linuxjc/3412.html, 
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
## 安装python环境
到官网下载[Linux版](https://www.python.org/downloads/source/)，可参考[安装过程](https://www.jianshu.com/p/6059f7fc2cd0)，可能需要改变python的[符号链接](https://www.cnblogs.com/travellife/p/4064183.html)，最后安装[虚拟环境virtualenv](https://www.cnblogs.com/zh605929205/p/7705192.html)，也可采用python标准库的[venv](https://zhuanlan.zhihu.com/p/81568689)。
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

## ROS
- 安装ROS
  - 更新系统软件库
  ```
  $ sudo apt update
  ```
  - 安装ROS全功能版
  ```
  $ sudo apt install ros-kinetic-desktop-full
  ```
  - 查看可使用的包
  ```
  $ apt-cache search ros-kinetic
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
```
$ roscore
$ rviz
```

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
# 小工具
## ubuntu下分解大压缩文件或解压拆分后的文件
参考网址[https://blog.csdn.net/xunan003/article/details/79068648](https://blog.csdn.net/xunan003/article/details/79068648) 