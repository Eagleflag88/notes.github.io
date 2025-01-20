---
share: true
---

# Problem

- 之前的工作主要聚焦在加强detection的性能，以期获得更好的topo reasoning的表现；


# Architecture

## Feature Extraction

- PV → Multiscale Feature Extraction → PV Feature, $F_{pv}$；
- $F_{pv}$ → View Transformation → $F_{bev}$; 

## Lane Decoder

- Input:
	- Lane Query
	- $F_{bev}$

## Geometric Distance Topology
- Input
	- Lane
- Process
	1. 计算lane的起点和终点之间的距离矩阵，$D$；
	2. 基于高斯函数的映射$G_{Dis} = f(D)$；
- Output
	- $G_{Dis}$，一个跟$D$一样的Matrix

## Lane Similarity Topology

- 通过计算lane之间的相似性来推断车道拓扑；
- 越相似表示连接的可能性越高；
- Input：Lane Query
- Process
	1. 用MLP对Lane进行映射，获得$Q_{embed}$；
	2. 计算$Q_{embed}$之间的内积然后进行sigmoid操作；
- Output：
	- NxN的矩阵，$G_{sim}$；

# Training

## Loss

- $L_{det}$：focal loss for classification and L1-loss for regression
- $L_{topo}$：$G_{sim}$的loss

# Code

- https://github.com/Franpin/TopoLogic