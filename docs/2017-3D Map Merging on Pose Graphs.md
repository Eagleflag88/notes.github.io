---
share: true
---

# Sensor
Lidar + IMU

# Concept 
- 遍历另一个点云的轨迹点，在每一个轨迹点搜索匹配对（pose级别的）
- 找到一个匹配对之后，更新那个被匹配中的pose；
- 这样，不断进行匹配和传播，并更新pose，最后获得一个pose之间的匹配关系；

# Map Alignment
利用pose graph理论构建一个优化问题，解出最优轨迹；

# Map Update
利用pose和周围的local map进行地图旋转和构建；