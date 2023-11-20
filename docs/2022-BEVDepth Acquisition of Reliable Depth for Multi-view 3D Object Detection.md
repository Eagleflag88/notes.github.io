---
share: true
---
# Problem

- 现在的基于LSS的方法的深度估计效果不好;
- Depth Module Over-fitting: LSS没有对深度的直接监督，导致其学习到的模型泛化性能不好；
- 不准确的深度估计导致后续的pillar pooling的语义输入不准确；

# Approach

- 利用Lidar数据explicit的监督深度；


# Architecture

## Feature Extraction

- 利用CNN对图片进行特征抽取，$F^{2d}$；
- 选用ResNet；

## Depth Net

- Input
	1. $F^{2d}$；
	2. Camera内参；
- Process：
	1. 把Image Pixel和Cam内参利用SE网络组合起来；
	2. 利用网络预测pixelwise的深度分布；
- Output：$D^{Pred}$，$H,W,C_D$

## Depth Refinement Module
- To further enhance the depth quality；
- Input：
	1. $F^{3d}$；
- Process：
	1. Reshaping；
	2. Several 3×3 convolution layer；

## Voxel Pooling
- As usual
## Head


# Training

## DepthNet
- Depth GT: 利用Lidar+内外参生成像平面的深度真值；
- Loss:  Binary Cross Entropy;


