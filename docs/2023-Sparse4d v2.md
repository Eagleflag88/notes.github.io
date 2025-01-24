---
share: true
---
# Problem
 - Sparse 4D v1的时序融合模块的计算量随着历史帧数量线性增长，负担很大；


# Approach
- 使用recurrent的方式融合历史帧。也就是说本帧只融合上一帧的feature；

## Instance

- anchor：对象的物理性质，位置，方向，...；
- anchor embedding：轻量网络对anchor进行编码；dim = 256
- instance feature：从图像获得的语义特征；


# Architecture

## Image Feature Encoder
- 跟[[2023-Sparse4d#Image Encoder|2023-Sparse4d > Image Encoder]]一样；

## Single-frame layer
- 主要用来处理一些新生成的instance；
- Input
	- 初始化一系列anchor box和instance vector；总共900个instance，其中包含600个temporal instance；
	- Image Feature；
	- 相机参数
- Process
	- Deformable Aggregation：结构跟[[2023-Sparse4d#Refinement Module|2023-Sparse4d > Refinement Module]]里的Deformable 4D Aggregation一样，有两点不同：
		1. 使用更好的cuda实现；
		2. 显式的使用Camera Parameter计算feature的权重；
	- FFN + Refinement
- Output
	- Top K anchors and corresponding features

## Multi-frame layer

- Input
	- Top K instance from single-frame layer
	- Instance from previous frame -> Instance Temporal Propagation -> 本时刻的instance
- Process
	- Cross-attention：single-frame输出的instance as Q，上一帧的instance作为K和V；
	- Self-attention：Cross的结果做自注意力；
	- Deformable Aggregation + FFN + Refinement；
- Output
	- K个instance

# Training

## Dense Depth Supervision

- 对image feature用lidar点云进行监督。解决image encoder训练不稳定的问题；
- image feature -> 1x1 conv -> depth prediction -> 相机焦距 -> 物理空间深度；
- Loss：L1 loss

# Ref
- Sparse4D v2 Recurrent Temporal Fusion with Sparse Model.pdf

# Code

- https://github.com/linxuewu/Sparse4D
