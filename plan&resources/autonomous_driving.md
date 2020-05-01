# Autonomous Driving Reading

## Summary
对于自动驾驶而言, 定位主要靠高精度IMU+GPS(RTK), 而不是点云配准(可以想象点云的数据量). 且主要挑战在于障碍物的检测、行人车辆路径预测, 从而指导决策. 不同的传感器: 激光雷达、毫米波雷达、相机, 各有各的优势和弱点, 通过Fusion, 来综合解决这个难题.

## Reference
[传感器, 感知, 地图定位和规划控制的水平](https://zhuanlan.zhihu.com/p/55581334)
[自动驾驶中相机和LiDAR数据融合方法与目标检测](https://zhuanlan.zhihu.com/p/50654088)
[自动驾驶多传感器感知的探索](https://zhuanlan.zhihu.com/p/77859086)