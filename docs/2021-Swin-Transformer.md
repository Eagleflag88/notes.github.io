---
share: true
---
# Ref
- Original Paper: Swin Transformer: Hierarchical Vision Transformer using Shifted Windows

# Problem
- 视觉模态中我们需要关注各个尺度的问题；
- Higher resolution of pixels lead to the problem of computation complexity, which is quadratic to image size; 这里主要是指ViT的问题；

# Approach
- 提出一个hierarchical的transformer结构。通过shifted windows这个block实现；
- Limiting self-attention computation to non-overlapping local windows while also allowing for cross-window connection
- Hierarchical feature maps and has linear computational complexity to image size; 

# Architecture

## Patch Partition
- 把RGB划成一个个Patch（4x4），而且不能重合；
- 每个Patch相当于一个token，他的特征就是pixel的cat；
- 然后通过linear embedding layer投影到一个维度C；

## Swin Transformer block
- 本文最重要的模块是Window-based MultiHeadAttention模块；
- 其中，输入特征图会被均匀的划分为MxM个不重叠的Patch，然后我们只在Patch内部做Attention操作，这样计算量的增长不会像传统transformer一样随着特征图解析度的平方增长，而是线性增长；
- 每个MSA模块之后会接上一个LayerNormlization + 两层的MLP。MLP的激活函数使用GeLU，详见[[Deep Learning Basics#Activation|Deep Learning Basics > Activation]]；

## Successive  Swin Transformer blocks
- 在整个结构之中，我们会交替使用普通和Shifted-WMSA；
- Shifted-WMSA是W-MSA经过Patch Partition Shift之后得到的。窗口会被沿着图像的垂直和水平方向移动一定距离。这种移动窗口的机制允许跨越窗口边界的交互，同时保持计算量相对固定；
- Shifting带来的extra计算开销可以通过论文里提出的Cyclic Shift解决；
- 值得注意的是，这里attention的计算过程中会使用relative postion bias。详见[[Transformer#Relative Position Bias|Transformer > Relative Position Bias]]

## Patch Merging
- 把相邻的patch（2x2）连接成一个patch；
- 使用一个线性层把通道数提升到4C；
- 这样特征图上的patch数量减少到1/4，解析度降低1/2；

## Pipeline
- stage 1：Patch Partition + Swin Transformer block
- stage 2, ..., n：Patch Merging + Swin Transformer block

