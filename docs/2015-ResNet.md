---
share: true
---

# Ref

- Deep Residual Learning for Image Recognition
- [ResNet50_Callgraph](https://1drv.ms/u/s!AoE-r_P7l4j3g4UzBhJaMvPuopq9og?e=YNsjQ2)

# Problem

- 模型随着深度增加性能降级：
1. 随着模型深度增加，训练集精度先饱和，然后迅速下降；
2. 不是由于overfitting引起的，因为训练集精度也降低了；
- 实际上更深的模型应该能够产生更好的结果，比如说把一个模型变为两个部分，高层变成identity mapping，结果应该比单独使用更低的层的效果更好，但是事实上并不是如此，所以结论是现在的solver不够好；

# Approach
让模型explicitly拟合输出和identity mapping的差，按如下公式：

$$
H(X) = F(X) + X
$$

其中$X$是输入， $H(X)$是我们最终想要构建的mapping。$+X$是Identity Mapping。

# Architecture
- 总的架构来说，ResNet由两个部分组成：
	1. Stem：大的Conv Filter + Max-Pool；
	2. Residual Block：实现卷积操作+跳跃链接；
- 按照总共卷积层数的不同，在具体的ResNet的实现一般会有18，50和101层。

## Stem
- 采用经典的Conv+BN+ReLU组合，最后使用一个max-pool进行Down-Sampling；

## Residual Block
- 每个残差块包含两个或三个卷积层，这些层的输入通过一个“快捷连接”（shortcut connection）跳过这些卷积层并直接添加到卷积层的输出。
- 瓶颈设计（Bottleneck Design）：在较深的ResNet版本（如ResNet-50及以上）中，每个残差块使用“瓶颈”设计，其中包括一个1x1卷积层（用于降维），一个3x3卷积层（用于处理），然后是一个1x1卷积层（用于升维）。这有助于减少计算量。
- 整个网络结构由多个层组成，每个层里面堆叠了若干个Residual Block；

## Global Average Pooling

- 在网络的末端，ResNet使用全局平均池化层而不是全连接层来减少模型参数数量和过拟合的风险。


