---
share: true
---
# Sensor

Mono + IMU + GNSS

# Single-session
- Mono -> IPM -> 路面元素提取（非深度学习方法，不考虑子类，比如说车道线，箭头等等） -》 路面元素点云 -> 基于odometry(IMU + GNSS)进行点云堆积 -》 成图

# Multi-session
- Map Alignment: 直接在多趟地图之间通过ICP获得最优变换，然后直接在地图点云层面进行拼接
- Map Fusion: 通过最邻近获得地图上的匹配点对，其余的点就是独特点；然后使用一些前面步骤获得的关于地图点的不确定性的指标来过滤不确定度高的点；然后更新地图，输出融合后的地图