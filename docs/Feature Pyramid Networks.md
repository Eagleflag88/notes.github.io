---
share: true
---

# Ref

Original Paper: Feature Pyramid Networks for Object Detection.pdf

# Problem
- Object Detection任务需要检出多尺度的物体；使用单层的

# Current Solutions
- 方案1：构建图像金字塔，在每一层上面进行检测。问题是训练和检测都需要计算资源；
- 方案2：使用CNN的中间层来当作多个尺度上的特征图；这个方案的缺点是我们可能会误用低级别的特征；

# Concept
- 本方案的目标是naturally leverage the pyramidal shape of a ConvNet’s feature hierarchy while creating a feature pyramid that has strong semantics at all scales. 

# Architecture

FPN把单尺度的图片作为输入，通过Bottom-up Path，Top-down Path和lateral connection，最后输出多个尺寸的feature map。

## Bottom-up Path
- 按照一个CNN的Backbone计算，输出多个尺度的特征图。尺度步长定为2；
- 具体来说我们使用ResNet的Residual Block的激活输出作为特征图。并记为$C_2$，$C_3$，$C_4$，$C_5$；
- 这里只有卷积操作导致的特征图不断的抽象化，而没有尺度之间的交流，不能跟Bottom-up的尺度交流混淆；

## Top-down Path
- 通过上采样获得一层层的解析度越来越高的特征图；
- 具体来说一个Building Block接受上层的feature map作为输入：
	1. 进行2倍的上采样（nearest neighbour upsampling）;
	2. 对Bottom-up Path的同一层的特征图进行1x1的卷积操作，减少通道数 → Lateral Connection；
	3. 卷积的输出和上采样的输出进行相加融合；
	4. 对融合之后的特征图使用3x3卷积；
- 融合之后，每一层都会输出一个一样大小的特征图，记为$P_2$，$P_3$，$P_4$，$P_5$；

## Head
在每一个尺度的特征图上，我们都会进行Anchor或者RoI的提取，以进一步进行后续任务。