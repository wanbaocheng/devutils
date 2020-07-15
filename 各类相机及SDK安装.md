## Microsoft Azure Kinect DK
安装：[ubuntu16.04（非官方支持）](https://blog.csdn.net/denkywu/article/details/104652710),[ubuntu18.04（官方支持）](https://docs.microsoft.com/zh-cn/azure/kinect-dk/sensor-sdk-download)

开发文档官网：https://docs.microsoft.com/zh-cn/azure/kinect-dk/

## Astra 相机
SDK和开发文档：有多种平台多种版本，参见[官网](https://orbbec3d.com/develop/)

## intel realsense 相机  
参见[官网](https://github.com/IntelRealSense/librealsense), [releases](https://github.com/IntelRealSense/librealsense/releases),
[源码安装](https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md)

-- 在ros包中设置相机参数示例  
在ros包中的base_realsense_node.cpp中的registerDynamicOption函数中加入下面代码:
```python
if(sensor.supports(RS2_OPTION_VISUAL_PRESET))
    sensor.set_option(RS2_OPTION_VISUAL_PRESET, RS2_L500_VISUAL_PRESET_SHORT_RANGE)
if(sensor.supports(RS2_OPTION_PRE_PROCESSING_SHARPENING))
    sensor.set_option(RS2_OPTION_PRE_PROCESSING_SHARPENING, 3)
```
其中set_option函数参见librealsense2包中的rs_option.h.