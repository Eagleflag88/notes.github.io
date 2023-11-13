---
share: true
---
# Sensor

Lidar + IMU

# Concept

- 分析感知结果，判断容易退化的场景：使用ICP配准时的解的性质，基于eigenvalue
- 回环检测：
	1. 沿着轨迹生成submap，然后转化成2D占据栅格图，在其上提取ORB特征，生成潜在回环
	2. 根据潜在匹配计算ICP获得位姿，提取
	3. 使用PCM来剔除误匹配

- 没有明确的Map Update功能，应该是直接累积的