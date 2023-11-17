---
share: true
---
# Sensor

Vision + Lidar + IMU + Wheel

# Map Structure
- multi-modal factor graph
- 点+边
- pose和landmark是点，各种约束作为边
- Landmark: 视觉特征地图点，三角化获得；激光点云直接获得

# Single Session
- Mapping：vio，使用上面的factor graph进行优化；相对约束（Odometry based, 或者回环）和绝对约束（GPS）都可以加进来；
- 隔一段距离会生成新的地图，生成的地图会保留上一个地图的最后一个点，以避免不连续

# Multi Session
- Mapping Server: 处理multi robot mapping的问题
- Submap Preprocessing：大map由submap组成，每个submap首先会被预处理，包含每个submap内部的回环检测
- Multi Robot Processing：在大map的范围寻找回环，然后进行优化

# 语义回环
- 使用深度学习检测landmark，然后提取描述子；
- 找到匹配对里匹配对最近的pose被认为有回环

# Map Update
没有明确说明方案；