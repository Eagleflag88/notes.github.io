---
share: true
---
# Sensor 

Lidar + IMU

# Single-session
- Lidar帧-> 语义分割 -> 各种物体级别的聚类 -> 使用PCA生成线和面的方向 -> 进行关联 -> BA优化建图

# Multi-session
- Map Alignment：1 沿着submap的轨迹把地图分成一个个Block 2 使用GraffMatch算法来对线和面进行匹配，利用CLIPPER的框架得到robust的匹配 3 构造优化问题获得block之间的配准结果，这里类似ICP，进行了多次迭代；4 使用PCM消除false loop
- Map Update: 1 先优化一次pose，消除重复的landmark（利用距离判断是否重复）2 构造BA同时优化pose和landmark