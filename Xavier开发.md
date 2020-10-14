## Xavier刷机(Ubuntu18.04LTS)
- [下载sdkmanager](https://developer.nvidia.com/embedded/jetpack)  
  需要注册nvidia账号  
- 运行sdkmanager
```shell script
$ sudo apt install ./刚下载的sdkmanager??.deb文件名
$ sdkmanager
```
## 中文输入法
- 设置  
打开 System Settings -> Text Entry, 点击窗体左下角的 + , 添加 Chinese, 重启即可.
- Fcitx输入中文不显示候选词框  
```shell script
$ sudo apt remove fcitx-module-kimpanel
```
## [Jetson 系统监视程序Jtop](https://www.jianshu.com/p/497a9f6e34fd)
```shell script
$ sudo apt-get install python3-pip
$ sudo pip3 install jetson-stats
$ jtop
```
## [安装jetson-interface](https://blog.csdn.net/weixin_44457020/article/details/106951508)
参见 https://elinux.org/Jetson_Zoo
```shell script
$ git clone --recursive https://gitee.com/wanbaocheng/jetson-inference.git
$ cd jetson-inference/tools
$ sed -in-place -e 's@https://nvidia.box.com/shared/static@https://bbs.gpuworld.cn/mirror@g' download-models.sh
$ sed -in-place -e 's@https://nvidia.box.com/shared/static@https://bbs.gpuworld.cn/mirror@g' install-pytorch.sh
$ sed -in-place -e 's@https://github.com/pytorch/vision@https://gitee.com/wanbaocheng/pytorch_vision.git@g' install-pytorch.sh
$ sed -in-place -e 's@https://github.com/dusty-nv/vision@https://gitee.com/wanbaocheng/dusty-nv_vision.git@g' install-pytorch.sh
$ apt update
$ sudo apt-get install git cmake libpython3-dev python3-numpy
$ cd ..
$ mkdir build
$ cd build/
$ cmake ..
$ make -j$(nproc)
$ sudo make install
$ sudo ldconfig
```
示例:
```shell script
$ cd build/aarch64/images
$ imagenet-console bird_1.jpg bird_1_re.jpg
$ xdg-open bird_1_re.jpg
```
## Jetson AGX Xavier设置功耗模式及风扇转速
最佳性能设置
```shell script
$ sudo nvpmodel -m 0
$ sudo vim /sys/devices/pwm-fan/target_pwm  # 设置0-255之间的数字
$ sudo jetson_clocks
$ sudo nvpmodel --query
$ jtop
```
参见 https://blog.csdn.net/hlld__/article/details/108345609
## [ROS安装](https://elinux.org/Jetson_Zoo#ROS)
```shell script
# enable all Ubuntu packages:
$ sudo apt-add-repository universe
$ sudo apt-add-repository multiverse
$ sudo apt-add-repository restricted

# add ROS repository to apt sources
$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
$ sudo apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

# install ROS full
$ sudo apt-get update
$ sudo apt-get install ros-melodic-desktop-full

# add ROS paths to environment
sudo sh -c 'echo "source /opt/ros/melodic/setup.bash" >> ~/.bashrc'
```
## [Deep Learning Nodes for ROS/ROS2](https://github.com/dusty-nv/ros_deep_learning)
安装参见 https://github.com/dusty-nv/ros_deep_learning#installation

