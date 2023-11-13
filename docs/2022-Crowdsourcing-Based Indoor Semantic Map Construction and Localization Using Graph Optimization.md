---
share: true
---
# Sensor
Mono + IMU + Wifi

# Single-session

- 基本上使用VI-SLAM，然后加上wifi获得odometry；同时使用yolo v4检测环境中的landmark，加入到地图之中，形成一个单趟的稀疏语义地标的地图

# Multi-session
- Map Alginment：先通过地标匹配，优化生成一个地图点之间的变换，然后对其地图
- Pose graph optimization: 根据之前的数据匹配，优化pose，然后更新Map Element
- Map Update: 使用一套基于匹配和点质量（由匹配成功的次数决定）的landmark更新机制来更新地图