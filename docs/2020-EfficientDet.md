---
share: true
---

# Problem
- 之前的OD模型计算量太大；
- OD模型设计没有一种可以scale的方法，无法适应变化的需求；
- 之前的多尺度融合仅仅把多个尺度的特征图加起来，太粗暴了；

# Approach

-  We propose a weighted bi-directional feature pyramid network (BiFPN)；
-  Second, we propose a compound scaling method that uniformly scales the resolution, depth, and width for all backbone, feature network, and box/class prediction networks at the same time；
- 综上，我们设计了一个系列的object detector；

# BiFPN

BiFPN（双向特征金字塔网络）是FPN的基础上进行改进的一种网络结构，用于目标检测任务。BiFPN通过加强特征的上下文信息交流，提高了特征金字塔的特征提取能力。BiFPN的主要优点包括：

1. **双向交叉尺度连接**：传统的FPN是单向的，自上而下地进行特征融合，详见[[2017-Feature Pyramid Networks#Top-down Path|2017-Feature Pyramid Networks > Top-down Path]]。BiFPN引入了双向路径（自下而上和自上而下），允许信息在不同尺度的特征层之间更自由地流动；
    
2. **高效的特征融合**：BiFPN使用了加权特征融合，这意味着来自不同来源的特征会根据它们的重要性被不同地加权。这比简单的加法或者连接操作更有效；

BiFPN的工作机制可以大致分解为以下几个步骤：

1. **自下而上的路径**：这个路径从最低层（最高分辨率）开始，通过卷积和上采样（或池化）操作逐层向上传递特征，每一层都会融合来自下一层的特征。
    
2. **自上而下的路径**：这个路径从最高层（最低分辨率）开始，通过卷积和下采样操作逐层向下传递特征，每一层都会融合来自上一层的特征。
    
3. **加权特征融合**：每个节点的输出是通过一个加权求和的方式获得的，权重是可学习的，这使得网络可以自适应地优化特征融合的过程。
    
4. **多次迭代融合**：在EfficientDet中，BiFPN结构会被多次重复，以进一步增强特征。
    

在具体的实现中，这种结构允许不同分辨率的特征图更有效地融合，提供了丰富的语义信息和精细的空间信息，这对于检测不同尺寸的目标非常有用。BiFPN通过这种方式显著提升了目标检测任务的性能，同时保持了计算的效率。

# Architecture  

1. **骨干网络（Backbone）**:
    
    - **EfficientNet**: EfficientDet使用EfficientNet作为其骨干网络。EfficientNet是通过神经网络架构搜索（NAS）得出的一系列模型，它们在模型尺寸和计算效率之间进行了优化。这些模型被称为EfficientNet-B0到B7，数字越大，模型越大，精度越高，计算量也越大。
    - **输入**: 图像输入尺寸根据不同的EfficientNet版本而变化，例如，EfficientNet-B0可能从224×224开始，而B7可能从600×600开始。
2. **双向特征金字塔网络（BiFPN）**:
    
    - **特征融合**: BiFPN允许信息在不同尺度的特征层之间自由流动，这通过加权特征融合来实现，每个特征层都通过自下而上和自上而下的路径与其他层相连接。
    - **输入**: 从EfficientNet骨干网络中得到的多尺度特征图。
3. **分类和回归网络**:
    - 接收BiFPN的多尺度输出，利用全卷积层进行分类和回归；
    - **分类头（Class Head）**: 对于每个锚点，该网络预测目标的类别。
    - **回归头（Box Head）**: 对于每个锚点，该网络预测目标的边界框。
4. **锚点**:
    
    - **定义**: EfficientDet使用了锚定框（anchor boxes）的概念，这些锚定框有多种尺寸和比例，对应于不同尺寸的目标。
5. **预测**:
    
    - **输出**: 最终的预测包括目标的边界框位置、目标的类别及其置信度。这些是以锚点为基础，然后模型微调来的。

# Ref

- EfficientDet: Scalable and Efficient Object Detection

