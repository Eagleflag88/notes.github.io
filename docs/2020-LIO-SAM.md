---
share: true
---

# Sensor

- IMU
- Lidar
- GNSS

# Frontend

## Lidar
1. Feature Extraction(LOAM)
2. 挑选Keyframe（pose变换作为阈值）

# Backend

- Factor graph based Sliding window Optimization:

## IMU-Preintegration Factor
- 基本工作原理参考[[IMU Pre-Integration|IMU Pre-Integration]]
- IMU的Bias在后端一起估计；

## GPS Factor
- 如果和Lidar没有硬件同步则插值获得GPS测量值对应的pose；
- 只有当 estimated position covariance大于GPS的variance时才插入；

## Lidar Odometry Facotr
- 我们维持一个voxel local map,由n个关键帧的特征组成；
- 新的lidar scan跟local map作匹配；
- 利用匹配（LOAM的方法）构建优化问题获得新一帧的位姿；
- 新帧和旧帧之间的变换就是Lidar Odometry Factor；

## Loop Closure Factor
- 基于欧式距离的loop detection

# Code
- https://github.com/TixiaoShan/LIO-SAM.git

# Ref
- LIO-SAM: Tightly-coupled Lidar Inertial Odometry via Smoothing and Mapping