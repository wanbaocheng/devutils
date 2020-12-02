# 安装nvidia版的docker
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
# Docker常用命令  
- 查看Docker的客户端和服务器版本  
```shell script
$ sudo docker version
```
## 镜像
- 查看或搜索镜像
```shell script
$ sudo docker image ls
$ sudo docker images
$ sudo docker search 
```
- 拉取镜像
```shell script
$ sudo docker pull hello-world
$ sudo docker pull ubuntu:16.04
$ sudo docker pull nvidia/cuda:10.0-base
```
- 罗列镜像
```shell script
$ sudo docker image ls
```
- 删除镜像
```shell script
$ sudo docker image rm hello-world
$ sudo docker image rm $(docker images | grep "none" | awk '{print $3}')
```
- 保存镜像
```shell script
$ sudo docker save image_name -o /home/wbc/docker/name.tar
$ sudo docker save container_name -o image_name.tar
```
- 加载镜像
```shell script
$ sudo docker load -i image_name.tar  
```
## 容器
- 罗列容器
```shell script
$ sudo docker container ls
$ sudo docker container ls -a
$ sudo docker container ps -aq
$ sudo docker ps -aq
```
- 创建容器
```shell script
$ sudo docker container create --name=mycudaNo -it nvidia/cuda:10.0-base bash
$ sudo docker container create --gpus=all --name=mycudaYes -it nvidia/cuda:10.0-base bash
$ sudo docker create -v /etc/localtime:/etc/localtime:ro -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY \
  -e GDK_SCALE -e GDK_DPI_SCALE --name hellogui -it ubuntu:16.04 bash   # 创建可显示GUI的容器
```
- 启动容器
```shell script
$ sudo docker container start mycudaNo
$ sudo docker container start mycudaYes
$ sudo docker container start $(sudo docker ps -a | grep "Exited" | awk '{print $1 }') 
```
- 创建并启动容器
```shell script
$ sudo sudo docker container run --name container_name -it ubuntu:16.04 bin/bash
$ sudo docker run --gpus all,capabilities=utility --name test_py2so -it nvidia/cuda:10.0-base bin/bash
$ sudo docker run --gpus=all -p 8906:8906 --name bfe1 bfe:beta /root/bfe/interface/run_server.sh
$ sudo docker run -it --name bfe_dis -h BFE --privileged -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=:0 bfe:dev bash
```
注意：对于x11-unix，需要在宿主机上安装 sudo apt install x11-xserver-utils。
- 停止容器
```shell script
$ sudo docker container stop mycudaNo
$ sudo docker container stop mycudaYes
$ sudo docker container stop $(sudo docker ps -a | grep "Created" | awk '{print $1 }') 
$ sudo docker stop $(sudo docker ps -aq) # 停止所有容器 
```
- 删除容器  
```shell script
$ sudo docker container rm $(sudo docker ps -a | grep "Created" | awk '{print $1 }') 
$ sudo docker container rm $(sudo docker ps -a | grep "Exited" | awk '{print $1 }') 
$ sudo docker rm $(sudo docker ps -aq) # 删除所有容器 
```
- 在运行的容器中执行命令
```shell script
$ sudo docker exec mycudaNo nvidia-smi
$ sudo docker exec mycudaYes nvidia-smi
$ sudo docker exec -it mycudaYes bash
```
- 由容器创建镜像
```shell script
$ sudo docker commit -m 'message bala bala' -a 'author name' mycudaYes demo1:v1.0
```
## 容器和宿主机之间的数据传输
```shell script
sudo docker cp 宿主机目录或文件路径     容器名称:目录
sudo docker cp 容器名称:目录或文件路径  宿主机目录
```

## [Docker容器显示图形到宿主机屏幕](https://blog.csdn.net/ericcchen/article/details/79253416)  
Docker本身的工作模式是命令行的，但是如果运行在docker中的应用需要显示图形界面, 该如何能实现呢？
- 安装xserver
```shell script
$ sudo apt install x11-xserver-utils
```
- 许可所有用户都可访问xserver
```shell script
$ xhost +
```
- 创建并运行docker容器
```shell script
$ sudo docker create -h HN -v /etc/localtime:/etc/localtime:ro -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY \
  -e GDK_SCALE -e GDK_DPI_SCALE --name ubuntu_gui_demo -it ubuntu:20.04 bash 
$ sudo docker start ubuntu_gui_demo
$ sudo docker exec -it ubuntu_gui_demo bash  # root用户
$ sudo docker exec -u wbc -it ubuntu_gui_demo bash  # root用户
```
- 测试能否显示图像界面  
在上面创建的容器中执行如下命令
```shell script
# apt update
# apt install xarclock
# xarclock
```
此时在屏幕上可见一个时钟界面. 

## Docker使用非root用户
+ 在宿主机上  
  - 创建docker组
  ```shell script
  $ sudo groupadd docker
  ```
  - 将当前用户加入docker组   
  ```shell script
  $ sudo gpasswd -a ${USER} docker
  ```
  - 重新启动docker服务
  ```shell script
  sudo systemctl restart docker
  ```
+ 在容器上
  - 以root用户进入已有容器或者创建新容器  
  - 在容器中创建一个用户名（例如：wbc）
  ```shell script
  # adduser wbc
  ```
  - 在Sudoers列表里添加用户
  ```shell script
  # vim /etc/sudoers
  ```
  在文件末尾添加一行
  ```shell script
  wbc ALL=(ALL:ALL) ALL
  ```
  注意：如果没有sudoers文件，说明系统不支持sudo命令，需要先行安装
  ```shell script
  # apt install sudo
  ```
+ 用非root用户登录
例如：
```
$ docker exec -u wbc -it 容器名称 bash
```
## [Docker可视化工具](https://zhuanlan.zhihu.com/p/149693508)
LazyDocker是基于终端的一个可视化查询工具，支持键盘操作和鼠标点击。
相比Portainer来说可能不那么专业，不过对于开发者来说可能反而更加好用了。
因为一般开发者都是使用命令行来运行Docker，偶尔需要图形化查看的时候，就可以使用LazyDocker这个工具。

安装LazyDocker也非常简单，运行下面的命令即可。
```shell script
$ docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock \
  -v ~/.config/lazydocker:/.config/jesseduffield/lazydocker \
  lazyteam/lazydocker
```
当然如果发现LazyDocker挺好用，准备经常使用的话，还可以把它做成缩写添加到shell配置文件中，这样就可以将它变成一个简单的命令。
例如我用的是bash，就将下面这样添加到.bashrc文件中。以后就可以直接用lzd来调用LazyDocker了。
```shell script
alias lzd='docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock -v ~/.config/lazydocker:/.config/jesseduffield/lazydocker lazyteam/lazydocker'
```

## 在Docker中安装IDS相机驱动
```
$ docker run --name ids2 -h ids2 --privileged -v /tmp/.X11-unix/:/tmp/.X11-unix \
  --device=/dev/bus/usb/002/004:/dev/bus/usb/003/004 -e DISPLAY=:0 -it nvidia/cuda:10.0-base bash
```
# 附录
- 基础docker  
  根据[docker官网](https://docs.docker.com/install/linux/docker-ce/ubuntu/)所述步骤进行安装。
- 安装Nviida Container toolkit(如果需要cuda支持的话)
  根据[官网](https://github.com/NVIDIA/nvidia-docker)所述步骤安装。
- 加载bfe:beta镜像
```
$ sudo docker load -i bfe_beta.tar
```
- 启动bfe_server服务
```
$ sudo docker run -p 8906:8906 --name bfe_server bfe:beta /root/bfe/interface/run_server.sh # CPU版
$ sudo docker run --gpus=all -p 8905:8906 --name bfe_server bfe:beta /root/bfe/interface/run_server.sh # GPU版
```
其中8905端口可根据需要指定，8906是容器中的端口，保持固定。  
- 调用API服务
```
$ curl -X POST -F 'upfile1=图片网址1' -F 'upfile2=图片网址2' -F 'upfile3=图片网址3' 
http://运行docker的服务器IP地址:8905/measure_url/
```
