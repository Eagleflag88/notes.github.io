---
share: true
---
# Sensor
GPS + IMU + Mono + Wheel

# Single-session
- Orbslam → Local map with ORB-landmark
- 语义轮廓提取 → 物体级别轮廓，2D
- 不做回环检测，因为比较罕见
- 生成的local map包括keypose，感知，物体级别轮廓，2D等等

# Multi-session
- Map Alignment: orb-keypoint 匹配，然后获得inter-session回环；构建pose graph优化；
- Map Update：根据pose graph的优化结果，进行orb-feature匹配，然后利用所有信息进行BA，然后删除掉重复地图点；
- 语义地图构建：
	1. 遍历所有优化过的keyframe，把2D轮廓投影到3D平面，跟踪/来回检验3D物体存在性；
	2. 轮廓匹配使用了点和轮廓延申方向两个问题