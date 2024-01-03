---
share: true
---

# Sensor

- IMU
- Lidar

# Problem

- 现在的方法一般只能建稀疏的特征地图；
- Dense的方法计算资源消耗很大；

# Approach

- Directly registering raw points to the map and update map
- Maintaining a map by an incremental k-d tree data structure, ikd-Tree, 支持：
	- 高效搜索
	- Incremental updates

## State Estimation

- States
	- IMU: Rotation, Velocity, Position, 角速度Bias，加速度Bias
	- 外参
	- 重力方向
- System Equation:
	- Motion Equation: IMU的kinematic model
	- Measurement Model：Lidar Point到某个平面的距离；

- Propagation：利用IMU测量值预测States和他的Covariance；
- Lidar Deskewing：
	1. 利用IMU估计每个Lidar Point的位置；
	2. 把所有的Lidar Point时间戳调整到Scan的End timstamp;
- Residual Computation:
	1. 把本帧scan跟global map做匹配，利用最邻近搜索最近的5个点；
	2. 用这5个点fit出一个平面，求得他的质心和法向量；
	3. Residual定义为质心到平面的距离；
- Iterative Kalman Filter
	- 求得最优状态

## Mapping
- The optimized pose finally registers points in the new scan to the global frame
- Merges them into the map by inserting to the ikd-Tree at the rate of odometry

# Code

- https://github.com/hku-mars/FAST_LIO

# Ref

- FAST-LIO2: Fast Direct LiDAR-inertial Odometry
- FAST-LIO: A Fast, Robust LiDAR-inertial Odometry Package by Tightly-Coupled Iterated Kalman Filter