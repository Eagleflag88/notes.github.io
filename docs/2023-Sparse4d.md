---
share: true
---
# Problem
- 现在的基于BEV的dense方法需要2D转3D模块；
- BEV feature一般不能表达高度；
- 现存的稀疏3D模型没有利用时序信息；


# Architecture

## Image Encoder

- Image -> ResNet + FPN；
- 输入是最近的$T$时段内的Image；
- 输出是$T$时间段内的multi-view/scale/timestamp的image feature；$I$

## Decoder
- 是一个多层结构，每层都由refinement module和Depth Reweight Module组成；
- 最后输出3d anchor和分类结果；

### Refinement Module
- Input:
	- $I$
	- M个3D anchor boxes: Mx11。每个anchore由它的3d位置，大小，heading和速度构成；
		- M设定为900，3D anchors的坐标 are obtained by performing K-Means clustering on the training set；
	- Instance Feature：MxC。C是Feature的维度；
- Process:
	1. Preprocess：
		- 上面的instance feature之间做self attention；
		- 用anchor box做postional encoding；然后和self attention结果相加；
	2. Deformable 4D Aggregation：对每个时刻的每个Anchor box都生成K个keypoint。维度是MxKxTx3。分为固定和可学习的keypoint；
		- 固定的keypoint就是在立方体各个面的中心点；6个
		- 可学习的keypoint由instance feature经线性变化而来；7个
		- 然后利用本车的ego motion把上面生成的keypoint投影到各个时刻。最终形成一个4D的keypoint集合；
	3. Sparse Sampling：把上面的4D keypoint利用位姿和相机内外参投影到各个时间的各个view的feature map；
		- 对每个anchor输出向量$f_m$的维度是KxTxNxSxC。N是view的数量；
	4. Hierarchy Fusion:
		- 用instance feature经过线性变换生成weight序列；
		- 对一个$f_m$我们先融合他的scale和view；然后通过一个先性层融合timestamp
		- 最后输出更新后的instance feature；
- Output
	- 优化过后的M个feature instance，$F_{M}$；

### Depth Reweight Module
- 利用multiple MLPs with residual connections作深度网络，对每个instance feature预测一个离散的深度值；
- 深度值被用来重新计算instance feature的权重；

# Training

- Loss由bbox loss，class loss和深度loss组成；
- 深度loss的真值从gt_box的真值来，不需要dense的depth；

# Ref
- Sparse4D Multi-view 3D Object Detection with Sparse Spatial-Temporal Fusion.pdf

# Code
- https://github.com/linxuewu/Sparse4D