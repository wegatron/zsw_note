# SLAM
## Dataset
传感器类型: IMU, Lidar, Camera, GPS, RTK

* gz data set
IMU, Lidar

* kitti
Sensor Data: IMU, Lidar, Camera, Gps, RTK
Ground Truth: stereo matching and optical flow, 3D visual odometry / SLAM

* TUM数据集

## System
* system linux: visual box
* for visualization
[ORB-SLAM2 整套系统](https://www.jianshu.com/p/ad040b374b2f): ROS and Pangolin 
Lego-LOAM: ROS

小结: linux + ROS

## Lidar
1. odo
lego-loam
2. mapping

3. registration
> PoseMap Lifelong, Multi-Environment 3D LiDAR Localization
> Integrating Deep Semantic Segmentation into 3D Point Cloud Registration

## Reading Target
1. 激光lidar相关
* mapping 更加方便
* registration 更加稳定

<!-- 2. visual相关
* 特征法: orb slam, orb slam2
* 直接法: DSO
* 无人机: SVO
* VIO: VINS -->

3. 激光+visual+imu

## skills
* not to be defeated:
https://www.jianshu.com/p/ba7f68e20882
* momenta
Lidar SLAM
熟悉点云滤波、特征提取、边缘和面检测、分割、聚类算法；
熟悉多线激光雷达中的目标检测，识别，跟踪方法；
熟悉C/C++、Python等编程开发，熟悉PCL、OpenCV，具有良好的编程能力；
具有深度学习、点云语义分割的相关研究经验，在高水平会议或期刊发表论文者优先；
具有无人驾驶、机器人环境感知、高精度地图等项目经验者优先。
ref: https://www.jianshu.com/p/cfcc6296aa7b
* ailib
熟悉三维点云和网格几何处理、纹理贴图、网格分割等计算机图形学相关算法。
熟练掌握 Eigen、CGAL 等常见的数学计算或图形几何处理开源库者优先。
拥有大规模场景重建、LOD 技术、CUDA 开发等实际项目经验者优先。
https://www.zhipin.com/job_detail/b149f27b452addfa1Xx-096_EFI~.html?ka=search_list_9
* 地平线机器人
多视几何、非线性优化、视觉目标检测场景分割