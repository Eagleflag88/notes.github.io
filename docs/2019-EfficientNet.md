---
share: true
---

# Problem

Is there a principled method to scale up ConvNets that can achieve better accuracy and efficiency?
- Depth: Deeper means stronger ability to extract features, but they are harder to train;
- Width: Width brings fewer parameters, it can lose the ability to extract high level feature;
- Image resolution: brings improvement of accuracy;
- 单独的调整模型的某方面的结构结果不会太好，需要均匀的地调整整个结构；

# Approach

EfficientNet使用了一种复合缩放方法，均衡地缩放网络的所有维度（宽度、深度和分辨率）。

这个复合缩放方法由以下两部分组成：

1. 基线网络（Baseline Network）： 研究人员首先使用神经架构搜索（NAS）来开发一个高效的基线模型，称为EfficientNet-B0。这个基线模型被设计为在特定的资源约束下最大化准确率。
    
2. 复合缩放（Compound Scaling）： 一旦确定了基线模型，就使用一个复合系数来统一缩放网络的深度、宽度和分辨率。这个缩放法则是基于以下公式的：

$$
\text{depth}: d = \alpha^\phi
$$

$$
\text{width}: w = \beta^\phi
$$

$$
\text{resolution}: r = \gamma^\phi
$$

其中，$\phi$是用户定义的复合系数，决定了网络缩放的总量；$\alpha$，$\beta$和$\gamma$是根据原始网络调整得到的常数，它们控制着深度、宽度和分辨率如何随着 $\phi$的增加而缩放。

通过这种方法，EfficientNet系列包含了多个模型（从B0到B7），每个模型都是对基线网络的复合缩放版本，具有不同的复杂性和性能特点。EfficientNet模型在多个标准数据集上取得了当时的最先进性能，同时保持了较低的计算成本和参数数量。这些特点使得EfficientNet非常适用于需要在有限资源条件下运行高效深度学习模型的场景。

# Architecture

- EfficientNet-B0的结构以mobilenetv2为蓝本，经过缩放而来；

## Stem
- Conv3x3

## Building Block
- Inverted bottleneck
	1. 先通过逐点卷积进行通道降维；
	2. 再进行深度卷积进行空间特征提取；
	3. 最后再通过逐点卷积进行通道升维；
- Squeeze-and-excitation module：SE模块通过显式地建模输入特征之间的依赖关系，进一步提升模型的表示能力。
	1. Squeeze操作：将每个通道的特征图压缩成一个数值，即全局平均池化（global average pooling），可以理解为对每个通道进行特征提取，得到该通道的重要性系数。这个操作可以看作是对输入特征图的“压缩”，从而得到通道级别的全局特征。
	2. Excitation操作：根据Squeeze操作得到的通道重要性系数，对每个通道的特征图进行加权，即通过一个全连接层对重要性系数进行学习，得到一个权重向量，然后将该向量应用到原始的特征图上。这个操作可以看作是对通道级别的全局特征进行“激励”，使得重要的通道得到更大的权重，从而增强其在模型中的作用。

# Ref

EfficientNet: Rethinking Model Scaling for Convolutional Neural Networks

