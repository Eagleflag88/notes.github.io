---
share: true
---

# Architecture

## Image Encoder

- Perspective View Encoder: Image → PV Encoder（EfficientNet-B0） → PV Feature Map (HxWxK)，每个方向的图生成一个PV Feature Map，各个方向的图共享一个PV Encoder。
- Neural view transformer：PV Feature Map → 转换到相机坐标系（利用一个MLP） → 相机下的特征图 → IPM（相机外参）→ BEV下的特征图（各个方向的平均）
- Point Cloud Encoder：PointNet with dynamic voxelization.

## BEV Decoder
Image Feature + PointCloud Feature → fully convolutional network with ResNet as Backbone (一般用于语义分割) →  semantic segmentation + Direction prediction(预测每一个像素的方向，离散的方向，分类的loss) + Instance Embedding → 矢量化（在instance embedding的领域使用DBSCAN和NMS），然后按每个点的方向连接生成vector


# Training
没什么特别，一个多目标优化

# Evaluation Metric
IoU or Chamfer Distance

# Sensor
周视+Lidar

# Code
https://github.com/Tsinghua-MARS-Lab/HDMapNet.git