---
share: true
---
# Sensor

Lidar

# Single-session
使用lidar-slam模块生成地图和keyframe

# Multi-session:
- Map Alignment: LT-SLAM: 寻找session内部和跨session的回环，最后形成一个大地图，使用scan-context进行回环检测，使用robust优化算法来优化整体地图
- Map Update:
	1. 剔除动态物体导致的地图变化
	2. Change Detection: 通过检查新图元素一定范围内是不是包含k个旧图的点，来检出低动态的点，判断它是正还是负的变化；
	3. 实施这种变化