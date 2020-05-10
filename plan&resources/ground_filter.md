# Altitude Constraint Calculation
1. 地面检测
2. 二维栅格化, 地面点云聚类
3. 高程计算

## Ground Filter
### Question
* 应用场景是什么
    1. 地面坑洼
    2. 螺旋向上/下
    3. 坡道
    4. 多层
* 我们当前采集的激光数据有没有

### 数据获取
sensorData.h
SensorType.h

### reference
* CRF2 SVM or Decision Tree 
Learning a Real-Time 3D Point Cloud Obstacle Discriminator via Bootstrapping

* ?方法
> Fast Segmentation of 3D Point Clouds: A Paradigm on LiDAR Data for Autonomous Vehicle Applications
>https://github.com/VincentCheungM/Run_based_segmentation

* 高斯模型的方法
Gaussian-Process-Based Real-Time Ground Segmentationfor Autonomous Land Vehicles github
https://github.com/wavelab/libwave/tree/master/wave_matching/include/wave/matching

* mesh 的方法
>On the Segmentation of 3D LIDAR Point Clouds
>https://www.velodynelidar.com/lidar/hdlpressroom/pdf/papers/journal_papers/On%20the%20Segmentation%20of%203D%20LIDAR%20Point%20Clouds.pdf

* 分类的方法
> SqueezeSeg: Convolutional Neural Nets with Recurrent CRF for Real-Time Road-Object Segmentation from 3D LiDAR Point Cloud
> https://github.com/BichenWuUCB/SqueezeSeg
> https://zhuanlan.zhihu.com/p/43598102

* better分类的方法
> PointSeg: Real-Time Semantic Segmentation Based on 3D LiDAR Point Cloud
> https://github.com/ywangeq/PointSeg


* AutoWare ray ground filter
> https://github.com/CPFL/Autoware/tree/master/ros/src/sensing/filters/packages/points_preprocessor/nodes

* Fast segmentation of 3D point clouds: A paradigm on LiDAR data for autonomous vehicle applications
> https://github.com/VincentCheungM/Run_based_segmentation
