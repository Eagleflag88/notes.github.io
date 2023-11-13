---
share: true
---
# Sensor

GPS + IMU + Mono + Wheel

# Single-session
- Mono →  语义分割获得地面元素（车道线，箭头等等）→ IPM（其中车道线使用基于灭点的方法提高了精度）
- Mono → 物体检测获得空中元素（交通灯，牌）→ 利用光流进行跟踪 → 本车
- 单趟输出pose + 地面和空中元素

# Multi-session
- Map Alignment
	1. 点云对齐：使用semantic GICP，针对不同类型元素计算不同cost，进行点云配准，获得匹配，然后优化pose graph获得最优轨迹，对齐轨迹，同时，使用pcm来剔除外点匹配
	2. BA：同时优化pose和语义元素，其中对面元素，比如箭头和交通牌，使用点到面的距离，对线元素，我们使用点到线距离；具体实现细节参考文章：BALM: Bundle Adjustment for Lidar Mapping
- Map Upate：
	1. Clustering: 第一阶段：聚类生成一个lineobject；第二阶段：利用逻辑关系推理多个lineobject之间的关系；其他类型的元素直接使用距离来聚类获得结果
	2. 向量化：使用lineobject生成一个bezier curve；