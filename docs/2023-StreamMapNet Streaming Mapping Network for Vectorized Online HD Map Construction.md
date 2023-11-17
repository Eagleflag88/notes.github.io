---
share: true
---

# Problem
- Constrained by their reliance on single-frame input，with the absence of temporal information；
- Small perception range（60 × 30 m）；
- 普通的deformation attention无法捕捉奇形怪状的交通标识；

# Approach
- 使用multi-point attention；
- 利用 temporal information -> Streaming strategy for our temporal fusion, instead of stacking as in BEVFormer V2；

# Architecture

## BEV Feature Encoder

- Image → CNN-Backbone → Feature Pyramid Network for Feature Aggregation → BEV feature extractor to lift 2d feature to bev
- Output: BEV Feature

## Decoder

- Object Query:  each query encodes both semantic and geometric information of a map element instance，总共$N_q$个；
- Multi-Point Attention：
	1. 相比较传统的deformable attention，对每一个object query我们的采样点的坐标是绝对的，不是相对于object中心的坐标。
	2. 这样我们能够model more flexible, non-local attention region；适应交通标识的形状；

## Temporal Fusion

- Query Propagation：保留置信度最高的K个Query，在下一帧当作positional prior；
	1. 利用本车odom的变换和上一帧的$Q_{t-1}$输入一个MLP生成本帧的$Q_t$；
	2. 利用odom直接预测本帧的预测参考点；
	3. Decoder的输出Query会被前一帧的前K个Query替代；

- BEV Fusion
	1. Warping：利用ego motion更新上一帧的BEV Feature，$F_{t-1}$；
	2. Fusion：利用GRU网络与$F_{t-1}$融合；

# Training

## Labeling

使用Bipartie匹配Prediction和GT，思路还是跟MapTR一样。详见[[2023-MAPTR STRUCTURED MODELING AND LEARNING FOR ONLINE VECTORIZED HD MAP CONSTRUCTION|2023-MAPTR STRUCTURED MODELING AND LEARNING FOR ONLINE VECTORIZED HD MAP CONSTRUCTION]]
## Loss

- Polyline的Smooth L1 Loss
- 分类的Focal Loss
- Transformation Loss

# Implementation

- Backbone: ResNet-50
- BEV Feature Extractor: BEVFormer第一层。详见[[2022-BEVFormer|2022-BEVFormer]]
