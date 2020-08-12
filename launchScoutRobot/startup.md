# Scout 机器人启动介绍
## 硬件检查
1. 机器人底盘VGA线连接是否正常；
2. 机器人底盘旋钮和按键是否开启，两侧制动安全钮是否拔出；
3. zed camera usb接口；
4. xsens IMU usb接口；

## 软件配置
### usb设备rules文件编写
1. Scout和xsens usb设备rules放置于[`ai_robot/ai_robot_sensors/rules`](https://github.com/NLS-SJTU/ai_robot/tree/master/ai_robot_sensors/rules)
然后执行
```sudo cp AgileX-Scout.rules /etc/udev/rules.d && sudo /etc/init.d/udev restart
```
可以在`/dev/`显示`xsensUSB`和`ScoutUSB`
执行
```
ls /dev/ |grep USB
```
，修改.launch文件中的`port_name=`参数即可。

### usb设备驱动
1. [scout](https://github.com/westonrobot/scout_ros)
2. [xsens](https://github.com/xsens/xsens_mti_ros_node)
3. [zed](https://github.com/stereolabs/zed-ros-wrapper)
在catkin_ws工作空间中`catkin_make`即可

## 运行
1. `roslaunch scout_bringup scout_minimal_uart.launch `scout底盘
2. `roslaunch ai_robot_navigation justmove.launch `[手柄控制](https://github.com/NLS-SJTU/ai_robot_doc/tree/master/software/navigation)
3. `roslaunch zed_wrapper zed.launch `zed相机
4. `roslaunch xsens_driver xsens.launch`xsens IMU

## qudong
