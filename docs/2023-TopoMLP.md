---
share: true
---
# Problem

- 现有的topology对detection的依赖程度有多强？
- 现在的topology预测使用的是graph model

# Concept

- 遵循先检测后reason
- 使用MLP+Positional Embedding的方法来做topo reasoning

# Architecture

## Lane Detector

- 6 RGB → ResNet → 3D Positional Embedding → Control Point → Decoder 

## Traffic Element Detector

- 使用yolo v8来加强transformer-based Detector
- Front View 

## Lane-lane Topo

- Input
	1. lane coordinates
	2. lane query，$N_L \times C$，相当于有$N_L$个Lane Query

- Process
	1. lane coordinate → MPL Embedding 
	2. 上面的输出加上lane query的到$Q_L$，维度是$N_L \times C$
	3. 上面的操作重复$N_L$遍，得到两个feature；维度都是$N_L \times N_L \times C$
	4. 上面的feature经过一个Concat之后经过MPL获得topo prediction，维度$N_L \times N_L$，实际上是一个二元分类

- Output
	1. Lane-lane topology，$N_L \times N_L$

## Lane-Traffic Topo

- Input
	1. traffic query：$N_T \times C$
	2. lane query，$N_L \times C$，相当于有$N_L$个Lane Query

- Process
	1. Lane query + MLP(A)，A是从pv到bev的变换矩阵
	2. Traffic Query 和变换过Lane query的经过多次concat得到$Q_{LT}$，维度$N_L \times N_T \times 2C$
	4. 上面的feature经过一个Concat之后经过MPL获得topo prediction，维度$N_L \times N_T$

- Output
	1. Lane-traffic topology，$N_L \times N_T$

# Training

## Loss

- Lane Detection: focal + L1
- Traffic Detection: focal + L1 + GIoU
- Lane-Lane Topo: focal + L1 for matched lane point
- Lane-Traffic Topo: focal loss

# Experiment

- 


