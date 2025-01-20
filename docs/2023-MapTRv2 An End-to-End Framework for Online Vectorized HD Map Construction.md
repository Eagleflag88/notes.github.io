---
share: true
---
# Problem
- V1的训练收敛速度不够；
- V1推理计算量和显存消耗大；

# Approach
- Introduce auxiliary one-to-many matching to speed up the convergence；
- To reduce the cost of computation and memory, we introduce decoupled self-attention for interaction among queries, i.e., respectively performing attention along the inter-ins. dimension and intra-ins. dimension；
- we adopt auxiliary dense supervision on both perspective view and bird’s eyes view, 


# Architecture:
- Encoder: Feature Extraction (ResNet) + 2D-BEV Transformation （ LSS-based BEVPoolv2） -> BEV Feature （HxWxC）
- Decoder: 
	1. Decoupled Self-attention，performing attention along the inter-ins. dimension and intra-ins. dimension；

# Training

## One-to-one Prediction Loss
参考[[2023-MAPTR STRUCTURED MODELING AND LEARNING FOR ONLINE VECTORIZED HD MAP CONSTRUCTION#Training|2023-MAPTR STRUCTURED MODELING AND LEARNING FOR ONLINE VECTORIZED HD MAP CONSTRUCTION > Training]]

## One-to-Many Set Prediction Loss
参考[[2023-DETRs with Collaborative Hybrid Assignments Training|2023-DETRs with Collaborative Hybrid Assignments Training]]

## Dense Prediction Loss
- Depth Prediction Loss
	- GT: Lidar on perspective view；
	- Depth Prediction: depth prediction head depth on the PV feature map；

- BEV Segmentation Loss -> BCE Loss
	- GT: BEV Map；
	- Segmentation: dense segmentation；

- PV Segmentation Loss -> 
	- GT:render map GT on the perspective views with intrinsics and extrinsics of cameras；
	- Add a auxiliary PV segmentation head；

总体Loss就是上面三者之和；

# Sensor
周视 + Lidar

# Code
https://github.com/hustvl/MapTR.git



