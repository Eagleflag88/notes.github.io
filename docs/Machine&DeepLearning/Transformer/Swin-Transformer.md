---
share: true
--- 

# Concept
- 提出一个hierarchical的transformer结构。通过shifted windows这个block实现；
- limiting self-attention computation to non-overlapping local windows while also allowing for cross-window connection
-  hierarchical feature maps and has linear computational complexity to image size; ss

# Problem
- 视觉模态中我们需要关注各个尺度的问题；
-  Higher resolution of pixels lead to the problem of computation complexity, which is quadratic to image size; 这里主要是指ViT的问题；

# Architecture

## Patch-Partition
- 把RGB划成一个个Patch（4x4），而且不能重合；
- 每个Patch相当于一个token，他的特征就是pixel的cat；
- 然后把通过linear embedding layer投影到一个维度C；

## Swin Transformer block
- 在patch层面做self attention： 

## Patch Merging
- 把相邻的patch（2x2）连接成一个patch；
- 使用一个线性层把通道数提升到4C；
- 这样特征图上的patch数量减少到1/4，解析度降低1/2；