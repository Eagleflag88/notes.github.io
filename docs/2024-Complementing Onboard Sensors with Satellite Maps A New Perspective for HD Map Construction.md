---
share: true
---

# Problem
- 长距离的车端检测性能还不足
- occulusion会影响性能

# Idea

## 卫星地图
- 用卫星图片做补充
- 从googlemap下载nuscene覆盖区域的卫星地图
- 卫星地图和nuscene坐标粗对其
- 把卫星地图分成一块块

## 

# Architecture

## Vision BEV Extraction

- Ref: [[2023-MAPTR STRUCTURED MODELING AND LEARNING FOR ONLINE VECTORIZED HD MAP CONSTRUCTION|2023-MAPTR STRUCTURED MODELING AND LEARNING FOR ONLINE VECTORIZED HD MAP CONSTRUCTION]]
- Ouput: Visual BEV Feature

## Satellite Map BEV Extraction

- Input
	1. Map tile

- Process
	 1. U-Net with ResNet18 as Backbone

- Output

## Feature-Level Fusion

- 主要用来提取跟道路相关的部分

- Input
	1. 视觉BEV Feature
	2. 卫星BEV Feature

- Process
	1. 各自Postional Embedding + Linear Projection
	2. Cross Attention

- Output
	1. Refined BEV Feature

## BEV-Level Fusion

- 主要解决定位误差


- Input
	1. Refined BEV Feature
	2. 卫星BEV Feature

- Process
	1. Concat
	2. CNN预测出BEV Feature上每一个点的offset
	3. 根据上面的结果对卫星BEV Feature进行变换
	4. Concat with Refined BEV Feature

- Output
	1. Fused Feature

## Prediction Head


# Experiment

## Baseline
- Segmentation performance：HDMapNet
- Instance Detection: MapTR

## Ablation Study

- feature level fusion: 直接concat就能带来挺大提升，masked attention最好
- bev level fusion：微小提升

## Conclusion

- 跟MapTR比提升有限

# Code

- https://github.com/xjtu-cs-gao/SatforHDMap