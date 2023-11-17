---
share: true
---
# Problem
普通的DETR Assignment策略（one-to-one set matching）中，Query匹配到的正样本很少；

# Approach
- 使用one-many的匹配策略，where each groundtruth box is assigned to multiple coordinates in the detector’s output；
- 具体来说我们利用传统的one-many匹配来监督encoder的输出；

# Architecture
- Backbone + Encoder + Decoder；
- Collaborative Hybrid Assignments
	1. Memory → Scale Adaptor → 多尺度金字塔；类似与ViTDet
	2. 把金字塔的特征输入一些典型的目标匹配头中（ATSS, Faster R-CNN ...），然后进行匹配和Loss计算，获得监督效果；
- Customized Positive Queries Generation：
	1. 利用上面生成的正样本加上位置编码和线性变换生成给Decoder用的正Query；
	2. 这些生成的正Query跟其他正常路径生成的Query一起进入原始的分配方法（DETR）；

# Code
mmdetection