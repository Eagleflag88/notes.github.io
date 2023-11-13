---
share: true
---
# Sensor

Lidar/RGBD/IMU等等

# 回环检测：
- 抽取全局描述子（Scan Context/CosPlace/NetVLAD）来生成潜在回环
- 使用基于maximal algebra connectivity的算法来选择回环
- 使用基于局部特征的算法来验证回环间的几何一致性

# 后端
- 分布式
- 使用Graduated non-convexity算法来优化pose，这样避免了使用PCM来剔除回环外点

# Code
https://github.com/MISTLab/Swarm-SLAM