---
share: true
---
# Sensor

Lidar + IMU + Wheel + GPS

# Single-session
1. Dynamic Point Removal (3D detection，Afdetv2)
2. 激光去畸变：利用IMU-Preintegration，计算激光帧间pose变化；
3. Lidar Mapping (LIO-SAM) 
	- Initial Prior Pose through imu integration
	- Scan-matching using generalized-ICP among LiDAR frames
	- GPS
	- Loop Detection

# Multi-session
- Map Alignment: 
	1. 使用scan-context检测回环，包括inter-map和intra-map的回环
	2. 基于pose graph optimization进行轨迹优化
- Map Fusion：根据轨迹直接堆叠点云

# Annotation：
- Splitting：把上面生成的Map按照轨迹分成submap
- Detection：在submap上使用类似于MapTR的模型进行地面元素检测：线元素→ 多段线；规则形状元素 → 长方形（4个点）； 不规则元素 → 不规则多段线；最后获得地面标识都是矢量化的；
- Incremental Map Merging:
	1. 线元素：重叠区域大，端点接近 → 去除掉重叠区域；
	2. 长方形： 计算chamfer distance，当大于阈值，使用non-maximum suppression来融合；
	3. 不规则形状：计算IoU，当大于阈值，使用Union操作来融合；
- Point Sparsification: 使用Douglas-Peucker algorithm来对多边形和线进行降采样；

# Experiment

- 10231 urban scenes and 8334 highway scenes with map annotations are used to optimize the MapTR-based Unit Annotator
- MapTR
	- 50x50m的patch
	- 50个点每个元素

# Code

https://github.com/hustvl/VMA.git