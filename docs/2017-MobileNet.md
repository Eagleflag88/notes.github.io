---
share: true
---

MobileNet是由Google的研究者Andrew G. Howard等人在2017年提出的一系列为移动和边缘设备优化的网络架构。MobileNet模型主要针对的是在计算能力、存储空间和能耗受限的设备上运行高效的深度学习模型。MobileNet架构的关键特点在于它的轻量级，这是通过使用深度可分离卷积（Depthwise Separable Convolution）实现的。

# 深度可分离卷积

深度可分离卷积是MobileNet的核心，它将标准卷积分解为两个部分：深度卷积（Depthwise Convolution）和逐点卷积（Pointwise Convolution）。

1. 深度卷积：在这一步中，独立地对输入的每个通道应用一个卷积核。这意味着如果输入有32个通道，就会有32个卷积核，每个卷积核只在相应的单个通道上进行卷积。这个操作可以提取空间特征，但不会组合来自不同通道的特征。
2. 逐点卷积：这是一个1x1的卷积，其目的是组合由深度卷积处理过的特征。通过这种方式，可以跨通道学习特征，并增加模型的非线性。

# 轻量化和效率

MobileNet使用这种深度可分离卷积来大幅度减少模型的参数量和计算复杂度，这使得它在保持合理准确率的同时，变得非常轻量和高效。

# 超参数

MobileNet引入了两个重要的超参数，允许模型设计者根据应用场景的需要在延迟和准确率之间权衡：

1. 宽度乘子（Width Multiplier）：一个用于根据比例因子缩放网络中每层的通道数。这允许模型的大小和计算需求按比例缩放。  
2. 分辨率乘子：允许输入图像的分辨率按比例缩放，进一步减少计算量。
    

# 应用

MobileNet系列适用于多种任务，包括但不限于：

- 图像分类
- 目标检测
- 人脸识别
- 姿态估计

MobileNet的设计哲学是在保持高效率的同时提供足够的准确性，使得它在移动设备、嵌入式系统和IoT设备中非常受欢迎。随着MobileNet系列的发展，例如MobileNetV2和MobileNetV3，它们引入了更多的改进，如线性瓶颈层、倒置残差结构等，以进一步优化性能和效率。

# Ref

- MobileNets: Efficient Convolutional Neural Networks for Mobile Vision Applications