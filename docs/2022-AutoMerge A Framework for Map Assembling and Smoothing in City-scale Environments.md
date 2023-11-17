---
share: true
---
# Sensor
Lidar + IMU

# Offline Mapping

- 特征提取: Lidar scan → Accumulation → Dense Submap → PointNet VLAD(俯瞰) + 环绕特征 → 自注意 + 相互注意 → 512维的特征向量；
- Adaptive回环检测（在不同segment之间进行）：每个segment包含多个submaps（5m一个），对每个submap提取特征向量，在seqslam的框架下进行回环检测，然后使用ransac剔除外点
- Map Alignment：没有特别提及，应该是基于pose graph optimization
- Map Update: 没有特别处理，应该是直接堆叠