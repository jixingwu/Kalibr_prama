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
```
sudo cp AgileX-Scout.rules /etc/udev/rules.d && sudo /etc/init.d/udev restart
```
可以在`/dev/`显示`xsensUSB`和`ScoutUSB`
执行
```
ls /dev/ |grep USB
```
，修改.launch文件中的`port_name=`参数即可。

### usb设备驱动
1. [scout](https://github.com/westonrobot/scout_ros)


2. [xsens_imu](https://github.com/xsens/xsens_mti_ros_node)


3. [zed](https://github.com/stereolabs/zed-ros-wrapper)


4. [GPS](https://github.com/ros-agriculture/ublox_f9p)
+ 关于GPS：
	+ GPS通信波特率：模块当前实际波特率为115200，启动gps模块前需要在 /ublox_f9p/ublox_gps/config/zed-f9p.yaml 中设置baudrate参数，确保与模块实际波特率一致。
	+ gps模块的**天线要连接好**，不接天线会造成读数全为0。
	+ 关于模块波特率的**更改**和**获取**：在windows平台上下载ublox的官方配置软件[u-center](https://www.u-blox.com/zh/product/u-center)进行更改，具体方法参考u-center user guide：
		+ 首先选择view->Message View调出窗口;
		+ 进入UBX-CFG-PRT选项
		+ 在当前Message View的窗口下，点击Poll**可获取模块当前波特率**等信息；同时也可以选择新的波特率，并点击Send给模块**重新写入新的波特率**配置信息。
  (注：此时配置的信息仅对该次使用有效，想使配置永久生效需要将配置信息写入模块Flash，操作见下一条)
		+ 保存模块配置信息：在当前Message View窗口下，选择UBX-CFG-CFG，选择Save current configuration，并点击窗口下方的Send，此时配置信息将被写入Flash。
 


  



**注意:** 若对驱动文件有改动，需要在catkin_ws工作空间中使用重新编译：`catkin_make`




## 运行
1. `roslaunch scout_bringup scout_minimal_uart.launch `scout底盘
2. `roslaunch ai_robot_navigation justmove.launch `[手柄控制](https://github.com/NLS-SJTU/ai_robot_doc/tree/master/software/navigation)
3. `roslaunch zed_wrapper zed.launch `zed相机
4. `roslaunch xsens_driver xsens.launch`xsens IMU
5. `roslaunch ublox_gps ublox_zed-f9p.launch`  GPS


## 踩坑记录
+ 2020-8-18:
	+ 初始配置：走直线bag(最开始没有做初始化动作)+相机内参未修正+Tcam_to_imu的平移量符号反了
	+ 表现结果：报错初始化失败+轨迹跑飞（所有组合情况）
	+ 修正：重新测量了相机与imu中的各距离，并重新计算了变换矩阵，并据此修订了变换矩阵中的各参数，
	同时也纠正了T_cam_to_imu中平移量的符号和相机内参，并验证了imu各轴加速度方向、角速度方向是否与数据手册上标注相同，
	结果显示imu各轴与数据手册上标注相同，没有错误。此外还尝试了提高特征点数量。
	(PS:物体在竖直方向的加速度正方向与物体所受支持力方向相同，因此与重力加速度实际方向相反)
	+ 修正结果：双目相机+外参优化+无imu结果正常，双目相机+imu+外参优化结果跑飞
	+ 结论：imu参数有问题
