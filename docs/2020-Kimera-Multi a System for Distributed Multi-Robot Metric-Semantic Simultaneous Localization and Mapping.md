---
share: true
---
# Sensor

Mono + IMU

# Concept

- 分布式的系统，没有一个中央server来处理；
- 地图使用3D语义网格地图；

# Frontend:
- 使用Kimera算法，VIO基于orbslam的VIO，同时建立语义mesh地图；当两个机器人通讯时，进行跨机器人通讯，进行回环检测（基于BoW），检测成功后会基于pose之间的距离进行pose合并

# Backend:
- 利用回环，生成一个全局地图
	1. 回环剔除：使用Pairwise Consistency Maximization来剔除假回环，此处使用incremental的pcm版本；
	2. 使用回环帧和里程计约束构建pose graph，获得最优轨迹；其中求解器使用了Riemannian Block-Coordinate Descent (RBCD) solver
	3. 更新地图：构建类BA的cost来优化轨迹和网格地图的节点

# Code

https://github.com/MIT-SPARK/Kimera-Multi
https://github.com/MIT-SPARK/Kimera-Multi-Dat