---
share: true
---

# Problem

- Projection Lidar to Camera：损失几何信息，激光点云被拍扁到PV上了；
- Projection Camera to Lidar：把激光的点赋予camera来的特征，因为激光的稀疏性，损失语义信息；


# Approach

- 把Cam和Lidar的特征投影到BEV上，形成统一的描述；

# Architecture

## Feature Extraction
- Vision：Swin-T + FPN
- Lidar：VoxelNet

## Transformation

- Camera-to-BEV
	1. LSS-based
	2. 显式的预测camer-ray上的深度分布，生成一个camera feature point cloud，NHWD；
	3.  N is the number of cameras and (H, W ) is the camera feature map size.
	4. 进行BEV Pooling：即在BEV平面划分格子，然后聚合每一个格子里面的特征；

- BEV-Pooling
	1. Precomputation: 提前计算每一个camera feature point和他所属的bev voxel的关系，在内外参固定的情况下这是固定的；
	2. Interval Reduction：手写一个cuda-kernel并行聚合每一个bev voxel的特征；

- Lidar-to-BEV
	- 直接flatten

## BEV-Encoder
1. 把多路的特征直接相加/拼接；
2. 在BEV平面做卷积（ResNet），生成融合后的BEV Feature；

## Head
- OD
- Seg

# BEV-Pool V2
- 用视锥特征的索引作为视锥特征点影子，和根据相机内外参得到的体素索引一起离线预处理；
- 推理时根据这个视锥特征的索引去获取计算该视锥特征的图像特征和深度得分；
- 这样避免了计算存储和在线预处理视锥特征；
- 详见[知乎](https://zhuanlan.zhihu.com/p/586637783)

# Ref
- BEVFusion Multi-Task Multi-Sensor Fusion with Unified Bird’s-Eye View Representation







