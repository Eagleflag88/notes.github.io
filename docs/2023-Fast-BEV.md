---
share: true
---

# Problem

- 现在的方法要不速度慢
	- Query-based View Transformation → Attention模块需要的计算没有相应的硬件优化；
	- Depth-based transformation：最后的Unprojection + Voxel-pooling很非常耗时，而且只能在GPU平台优化；

# Approach

1. Fast-Ray transformation：
	- 轻量的视角转换模块
	-  Obtains BEV representations by projecting multi-view 2D image features to 3D voxels along the camera rays.
2. 使用FPN输出多尺度的2d图像特征；
3. BEV-Encoder：简单的纯卷积（ResNet）Encoder;
4. Strong data augmentation strategy such as flipping, rotation, resize, etc., for both image and BEV space;
5. Temporal fusion

# Architecture

## Image-Encoder

- Backbone（ResNet） + Neck（FPN）
- 输出多尺度的2D-Feature(3个尺度)

## Fast-Ray View Transformation
- 一个基本假设：一根camera-ray上的深度分布是均匀的；
- 预先根据摄像头内外参和上面的假设计算出3D空间的Voxel和2D图片像素点的对应关系，形成一个Look-up Table；
- 针对每一个尺度的2D特征利用Look-up Table计算出voxel的特征；
- 输出：multi-scale BEV features

## Temporal Alignment
- 根据本车运动把过去的Feature投影到本帧
- Concat

## BEV-Encoder
- 全卷积
- 利用卷积调整BEV Feature的维度
- 利用Concat融合多尺度信息；
- 输出： 单层BEV Feature

## Head

- 在BEV Feature上使用1x1 Conv进行检测；
- 输出：
	- Class
	- BBox Size
	- Direction
- 参考PointPillar

# Ref
- Fast-BEV: A Fast and Strong Bird’s-Eye View Perception Baseline


