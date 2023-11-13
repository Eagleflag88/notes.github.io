---
share: true
---

# Idea
1. 在BEV Feature中注入几何先验；
2. Piecewise Bézier Head

# Problem
1. 复杂的地图元素应该如何参数化；
2. 如何完成2D-3D转换（multi-view 2D images → 3D BEV）
3. Piecewise Bezier Curve: k个segment，每个segment是一个bezier curve（degress n）;


# Architecture
- Feature Extraction: CNN (ResNet50/SwinTR/EfficientNetB0) + FPN -> Camera Feature(HcxWcxKc);
- Semantic Decoder: 整体Encoder (2层) + Decoder (4层)的结构。流程：Camera Feature + BEV Query (HqxWqxKq，64x32，这个是学习出来的) -> Selff/Cross Attention -> BEV Feature (HbxWbxC) -> 1x1 Conv -> Upsampling -> Semantic Mask (HsxWsxU)，U是类别数量，基本上是分割结果。IPM-PE Align Layer；
- Instance Bezier Decoder: Instance Bézier queries (VxC, V是最大数量Instance, 60) + BEV Feature (HbxWbxC) -> Bézier descriptors (VxC) -> Instance-level smantic map (HsxWsxV)
- Piecewise Bézier Head: 
	1. Classification branch for number of pieces: FC + Softmax
	2. Regression branch for the coordinate of control points. 如果是同一种地图元素，

# Training
- Bezier Curve: Loss on Point/Curve/Region
- 在中间层加入Segmentation + Instance Supervision（很重要，提点很多）

# Evaluation
chamfer distance

# Sensor
周视

# Code
https://github.com/er-muyue/BeMapNet