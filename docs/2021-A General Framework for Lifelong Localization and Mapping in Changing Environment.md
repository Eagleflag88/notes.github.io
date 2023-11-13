---
share: true
---
# Sensor
Lidar + IMU + Wheel

# Single-session
- Lidar Odometry → 生成keyframe和submaps，每一趟会生成多个submap，地图是栅格占据地图

# Multi-session
- Map Alignment: 计算多帧之间的回环
- Map Update：
	1. trimming ： 当新地图和旧地图的重叠面积大于一个阈值，直接删除旧submap
	2. pose稀疏化：使用chow-liu算法稀疏化pose graph
- 优化：pose graph更新整体位姿