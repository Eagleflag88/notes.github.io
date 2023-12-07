---
share: true
---

# Problem

- 基于dense BEV feature的检测算法（比如说BEVDet，BEVFormer）效果好但是计算量很大；
- Sparse的检测算法（DeTR3D）效果比较差；
	1.  The vanilla MHSA has a global receptive field, lacking an explicit multi-scale design
	2. 没有处理image space和bev space里面物体的不同形态和大小；

# Approach

- 用BEV query替代稠密的bev feature；do not explicitly build a BEV feature

## Query Formulation

- Structure
	- Translation $[x, y, z]$
	- Dimension: $[w, l, h]$
	- Rotation: $\theta$
	- Velocity: $[v_x , v_y ]$
	- D-dim query feature
- Initialization
	- The queries are initialized to be pillars in BEV space where z is set to 0 and h is set to ∼4m;
	-  Other parameters $(x, y, w, l, \theta)$ are drawn from random gaussian distributions.

# Architecture

## Feature Extraction
- Backbone
- Neck

##  Scale-adaptive Self Attention

- Input
	1. Object Query
- Process
	1. Compute the all-pair distances between the query centers in BEV space
	2. 根据query获得它的感受野（这是学习出来的线性关系）
	3. 计算一个对query之间的距离也参与计算的multi-head attention
- Output
	- bev query

##  Adaptive Spatio-temporal Sampling

- Input
	1. Query after self attention
- Process
	1. 利用一个MLP（学习出来的）生成每个query的采样点；
	2. 利用query里的速度计算每个query前n帧的位置，注意这里每个query的速度都是不一样的；
	3. 利用本车运动变换所有的采样点的位置；
	4. 利用内外参把采样点投影到各个摄像头的view上，获得image feature；
- Output
	- bev sampling point
	- 对应的image feature

## Adaptive Mixing

## Head
- Class
- Regression

# Ref
- SparseBEV: High-Performance Sparse 3D Object Detection from Multi-Camera Videos
- [知乎](https://zhuanlan.zhihu.com/p/654821380)

