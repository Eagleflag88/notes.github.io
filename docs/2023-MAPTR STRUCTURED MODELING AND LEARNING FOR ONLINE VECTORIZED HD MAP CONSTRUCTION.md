---
share: true
---

# Idea
- 地图元素由点集（V）和可能的排列组合（Pi）表示。
- Polygon and Polyline，由有序点集组成。不同方向的地图元素的表示的是同一个地图元素，即所谓permutation-equivalence。

# Architecture:

## Encoder
- Feature Extraction (ResNet)，在Transformer Encoder的Attention模块中是Key和Value
- 2D-BEV Transformation (GKT/LSS)
- BEV Feature （HxWxC），在Transformer Encoder的Attention模块中是Query，其实就是代码中的BEV Query。最后输出的BEV Feature也是这个，可以理解为在每个BEV视角可能的坐标上都查询了一次；

## Decoder: 

- Input：
	1. 固定数量的ins queries + 固定数量的pt queries（这个是跟所有instance共享的）
	2. Encoder输出的BEV Feature;
- Process：
	1. Multi-Head Self Attention：Object Query；
	2. Deformable Cross-Attention: Object Query和Encoder的Feature；
- Output：
	1. BEV Feature

## Head
- Cls_Head
- Reg_Head

# Training
Classification Cost + Point2point Cost + Edge Dir Cost，他们有各自的系数。

# Inference
- 预测固定数量的地图元素
- Instance Matching：对每一个地图元素可以定义一个Ins_cost项，由类别cost（Focal Loss）和点集cost相加组成。点集cost利用点对点的曼哈顿距离。在多个预测和真值地图元素的可能组合之中使用hungarian algorithm找到ins_cost最小的组合。
- Point-level Matching: 点对点之间的距离，选择所有排列的cost最小的作为cost。

# Sensor
周视

# Code
https://github.com/hustvl/MapTR.git



