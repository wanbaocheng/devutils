### 1. 下载NVIDIA官方驱动
查看当前电脑的显卡型号
```
$ lshw -numeric -C display
```
然后到[官网下载](https://www.nvidia.com/Download/index.aspx?lang=cn)，选择对应型号和版本。

### 2. 安装驱动
进入Nvidia驱动文件所在目录
```
$ sudo ./Nvidia驱动文件名称
```
根据提示做相应选择即可。
### 3. 验证
```
$ nvidia-smi
```
显示驱动信息，说明安装成功。
