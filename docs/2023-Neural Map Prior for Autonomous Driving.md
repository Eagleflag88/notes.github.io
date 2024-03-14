---
share: true
---
# Problem
- 长距离的车端检测性能还不足
- occulusion会影响性能

# Concept

- Apply cross-attention, a mechanism that dynamically identifies correlations between current and prior features；
-  To update the global neural map prior, we utilize a learning-based fusion module that guides the network in fusing features from previous traversals；

# Architecture


- Input
	1. RGB
	2. Global Map Prior
	3. ego pose

- Process
	1. RGB → BEV Feature Extraction: $H \times W \times C$ + Positional Embedding
	2. 通过ego pose获得local map prior: $H \times W \times C$ + Positional Embedding
	3. C2P Cross Attention: Current(q) to Prior(k&v) Cross Attention → Refined BEV Feature, C = 256
	4. Map Update: Gate Recurrent Unit，用来更新全局地图
		1. 用local map prior和Refined BEV Feature作为输入
		2. 用2D CNN预测更新率和遗忘率
		3. 输出新的map prior
	5. 新map prior输出给decoder

- Output
	1. 静态元素


# Experiment

## Training

- 任务和基础算法保持一致
- freeze基础算法然后训练C2P和GRU

- Nuscene
- Baseline: HDMapNe BEVFormer

# Code

- https://github.com/Tsinghua-MARS-Lab/neural_map_prior