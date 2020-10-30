## Xavier刷机(Ubuntu18.04LTS)
- [下载sdkmanager](https://developer.nvidia.com/embedded/jetpack)  
  需要注册nvidia账号  
- 运行sdkmanager
```shell script
$ sudo apt install ./刚下载的sdkmanager??.deb文件名
$ sdkmanager
```
- STEP01 开发环境
  - 登录Nvidia开发者网站，此处需要有Nvidia Developer账号，没有的话需要注册。
  - 选择刷机设备的型号
  - 选择JetPack版本
- STEP02 详细说明和证书
- STEP03 刷机
  - 选择手工安装
  - 重新插拔电源线和数据线
  - 按照提示进行
- STEP04 汇总

## 中文输入法
- 设置  
  - 打开 System Settings -> Text Entry, 点击窗体左下角的 + , 添加 Chinese,
  重启即可.
  - 打开 System Settings -> Language Support, 点击 install
    按钮，把左侧列表框中的汉语提至列表最前列，键盘输入法系统选择 fcitx，
    重启系统
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

## 多目标追踪(MOT)
- [FastMOT](https://github.com/GeekAlexis/FastMOT)
  ```
  $ scripts/install_jetson.sh
  ```
  在安装过程中, 出现如下问题:
  - cuda.h找不到  
  ```
  $ vim ~/.bashrc
  ```
  在末尾添加(类似)
  ```
  export CUDA_HOME=/usr/local/cuda
  export PATH=$CUDA_HOME/bin:$PATH
  export LD_LIBRARY_PATH=$CUDA_HOME/lib64:$LD_LIBRARY_PATH
  export C_INCLUDE_PATH=$CUDA_HOME/include/:${C_INCLUDE_PATH}
  export CPLUS_INCLUDE_PATH=$CUDA_HOME/include/:${CPLUS_INCLUDE_PATH}
  ```
  参见
    - https://blog.csdn.net/ECNU_LZJ/article/details/104475098
    - https://blog.csdn.net/u011337602/article/details/87936331?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.add_param_isCf
    - https://blog.csdn.net/u011337602/article/details/81238164
  - 安装numba
  参见
    - https://bagustris.wordpress.com/2020/07/09/how-to-install-numba-and-librosa-in-jetson-agx-xavier-ubuntu-18-04/

- [multi-object-tracker](https://github.com/adipandas/multi-object-tracker.git)  
  - 安装
  ```
  $ git clone https://github.com/adipandas/multi-object-tracker
  $ cd multi-object-tracker
  $ python -m venv venv
  $ source venv/bin/activate
  $ pip install numpy matplotlib scipy
  $ pip install opencv-contrib-python
  $ pip install ipyfilechooser  # 如果使用jupyter例子(见examples目录)推荐安装. 若出现问题请参见本文档jupyter lab条目
  $ pip install -e .
  ```
  - 主要参考论文[High-Speed Tracking-by-Detection Without Using Image Information](http://elvera.nue.tu-berlin.de/files/1517Bochinski2017.pdf)
  - 在PC上安装通过, 但在AGX Xavier上安装opencv-contrib-python出错, 导致整个包无法安装
  
- [CINDA(CIrculation Network based Data-Association)](https://github.com/yu-lab-vt/CINDA)
  - 安装
  ```
  $ git clone https://github.com/yu-lab-vt/CINDA.git
  $ cd CINDA/src_c
  $ gcc cinda_funcs.c -fPIC -shared -o lib_cinda_funcs.so
  ```
  - 示例
  ```python
  import numpy as np
  from src_python.algo import *
  detection_arcs = np.loadtxt("./sample/detecton_mat.txt", delimiter=",")
  transition_arcs = np.loadtxt("./sample/transition_mat.txt", delimiter=",")
  traj, cost = mcc4mot(detection_arcs, transition_arcs)
  ```
  - 创建网络  
    参考
    - 论文: [Global Data Association for Multi-Object Tracking Using Network Flows](http://vision.cse.psu.edu/courses/Tracking/vlpr12/lzhang_cvpr08global.pdf)
    - 代码: 
      - https://github.com/ukiyoyo/py-mcftracker
      - https://github.com/nwojke/mcf
      
  - 论文
    - [Efficient Global Multi-object Tracking Under Minimum-cost Circulation Framework](https://arxiv.org/pdf/1911.00796.pdf)
    - [muSSP: Efficient Min-cost Flow Algorithm for Multi-object Tracking](https://papers.nips.cc/paper/8334-mussp-efficient-min-cost-flow-algorithm-for-multi-object-tracking.pdf)