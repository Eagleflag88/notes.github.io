---
share: true
---
# Problem 

- 之前的任务设计无法输出拓扑关系

# Concept

- 模型输出下面两个信息：
1. Lane topology graph comprising centerlines as well as their connectivity
2. Assignment relationship between lanes and traffic elements 

## Problem Formulation

- 车道中心线：有序3d点；
- traffic element：2d boundingbox；
- Topology
	1. 车道中心线：前后相连则为正
	2. 车道中心线和traffic element：bipartite graph,有关联则为正

# Architecture

## Feature Extraction

- PV → Multiscale Feature Extraction → PV Feature, $F_{pv}$；ResNet + FPN
- $F_{pv}$ → View Transformation → $F_{bev}$; 跟BEVFormer一样，但是不使用时序；参考[[2022-BEVFormer|2022-BEVFormer]]
- $F_{pv}$和$F_{bev}$都输出

## Traffic Element Decoder

- Input
	1. $F_{pv}$ as K, V
	2. $Q^{i-1}_t$ as Q, i是指decoder layer
- Process
	1. deformable transformer
	2. 
- Output

## Lane Centerline Decoder

- Input
	1. $F_{pv}$ as K, V
	2. $Q^{i-1}_l$ as Q
- Process
	1. deformable transformer
- Output

## Scene Graph Neural Network

- Input
	1. $Q_l$和$Q_t$

- Process
	1. Embedding Network:  extract semantic information from $Q_t$ and transform it into a unified feature space to match with centerlines；使用的是Graph Convolutional Network；
	2. 构建两个关系矩阵，分别描述车道中心线之间，车道中心线和交通灯

- Output
	1. $Q_l'$和$Q_t'$，深度256

## TE Head

- 2D BB
- Classification： Focal Loss
- Prediction：L1 Loss

## LC Head

- Line of 11 3D points
- Focal loss and IoU loss

## Topo Head

 - Input
	1. $Q_l'$和$Q_t'$

- Process
	1. 上面两个Query并行输入到MLP中；
	2. MLP的输出Concat
	3. Concat的输出 → MLP + Sigmoid Activation Function
	4. 感知head的匹配结果作为Assignment GT

- Output
	- 匹配结果，二元分类，通过Focal Loss来监督

# Experiment

- 使用OpenLane V2数据
- 重新实现STSU，MapTR和VectorMapNet来比较效果，使用一样的特征提取和视角变换模块；
- Result
	1. 在topo推理方面领先
	2. 在bev分割方面也领先

# Conclusion

- 没有使用时序信息

# Code

- https://github.com/OpenDriveLab/TopoNet.git

# Ref

- Graph-based Topology Reasoning for Driving Scenes.pdf
- OpenLane-V2 A Topology Reasoning Benchmark for Unified 3D HD Mapping.pdf