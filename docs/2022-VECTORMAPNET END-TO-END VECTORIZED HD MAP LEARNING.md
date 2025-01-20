---
share: true
---

# Idea
整个问题当检测问题来处理，预测若干个set of polylines，每个polyline是由若干个有序顶点组成。

# Architecture

## BEV FEATURE EXTRACTOR

- Camera：各个方向的图片 -> ResNet -> Feature Map -> transformation module （IPM based on homography）-> 四个不同高度的BEV Feature Map (HxWxC1)。
- Lidar：PointPillar Variant -> BEV Feature Map (HxWxC2) 
- 两个通道的特征叠在一起，形成BEV特征图

## MAP ELEMENT DETECTOR
- 类似DETR
- BEV特征图 + Element Query -> Transformer Decoder(6层) -> Keypoint Embedding -> MLP -> location + class。其中的Element Query是学习出来的，维度是kxd，d是embedding的维度(256)，k是地图元素包含的点的数量。

## Polyline Generator
- keypoint + label -> tokenize (as query) + bev特征图 -> transformer decoder(6层) -> polyline

# Training
- Detection Loss: like detr， keypoint classification, a smooth L1 loss, and an IoU loss for keypoints regression.
- Polyline generator loss:  log-probability of the polyline vertices。Using teacher forcing

# Evaluation
AP based on Frechet Distance

# Sensor
周视+Lidar

# Code
https://github.com/Mrmoore98/VectorMapNet_code.git