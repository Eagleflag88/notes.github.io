---
share: true
---
# Problem

- 很多车道线检测模型忽略了一个事实：车道线和车道中心线是互相绑定的，是一体的；
- 普通的detection方法只关注几何位置
- 中心线的方法没有物理对应实体

# Concept

- 引入LaneSegment，对lane的detection和topo关系采用统一的representation

## Lane Segment

- Geometric：由左中右三个线段组成，每个线段由多个3D点组成，有序3d点；
- Semantic：lane segment本身的属性 + 左右边界的属性
- Topological：graph（通过adjacency matrix描述），lane segment为顶点，边描述了连接性

## Lane Attention

 - Heads-to-regions mechanism
	 1. begin by distributing multiple reference points uniformly within the region of lane segment; 把参考点均匀撒在lane segment中；
	 2.  the multi-branch mechanism, where each head attends to a specific set of sampling locations within a local region

- Identical Initialization of Reference Points：
	1. In the first layer, every head adopts an identical reference point generated from the positional query
	2. Compared to the distributed initialization of reference points as conventional approaches do, i.e., initializing multiple reference points for each query

# Architecture

## Encoder

- ResNet + View-Transformer from BEVFormer

## Decoder

- Input
	1. Lane Segment Query

- Process
- One Layer of it
	1. self-attention
	2. cross-attention，使用Lane Attention
	3. feed-forward network
	- 把上面重复多次
- Output
	- 

## Predictor

- Centerline regression branch:  单边3d点+ offset branch → 左边和右边边界
- Semantic branch：classification of 3 class
- Topology branch: updated query features → MLP → weighted adjacent matrix for the lane graph

# Training

-  DETR-like paradigm

- Geometric loss: Manhattan distance
- Laneline type prediction:  cross-entropy loss 
- Topological loss: focal loss between lane graph

# Experiment

- OpenLane-V2
- 


# Code

- https://github.com/OpenDriveLab/LaneSegNet

# Ref

- LANESEGNET MAP LEARNING WITH LANE SEGMENT PERCEPTION FOR AUTONOMOUS DRIVING.pdf