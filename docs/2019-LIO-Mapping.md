---
share: true
---

# Problem

- Lidar数据稀疏，feature tracking容易丢；
- Lidar数据更新频率低；

# Approach

## IMU Pre-integration

## Lidar Pipeline

1. 利用imu来对lidar的一整个sweep去畸变；
2. 对一整个sweep提取特征，得到面和边缘特征点；
3. 利用KNN找到每个特征点与Local Map的匹配；
4. 计算每个平面的特征点的relative lidar measurement;

## Backend

- Window-based Optimization:
	- Prior Factor；
	- IMU Factor：基于Pre-integration 
	- Residual of the relative lidar constraints：平面特征点离平面的距离；
	- 输出pose
- Mapping
	- Registering the feature points to a global map；
	- 优化特征点的relative lidar measurement


# Code

- https://github.com/hyye/lio-mapping.git

# Ref

- Tightly Coupled 3D Lidar Inertial Odometry and Mapping