---
share: true
---
# Sensor
RGB

# Scene Representation
- 5D vector-valued function using a multiple perception layer neural network；
- Input：
	1. 3D Location；
	2. View Direction → 2 Dim；
- Output：
	1. Volume Density → 可以理解为这个点的透明度
	2. Radiance → 可以理解为颜色

# Rendering
- 从摄像头发射出的射线中采样一些3D点；
- 利用3D点的位置和2D的视角，利用MLP生成这些点的透明度和颜色；；
- 利用Volume Rendering的方法生成2D图片

# Implementation
- Positional encoding：Model the high-frequent part of the scene using triangular function；
	- 对位置和视角的输入都会先做一个位置编码，然后再输入到网络；
- Hierarchical volume sampling：训练一粗一细两个模型，通过粗模型获得合理的sampling分布；

# Training:

1. 数据准备：
    - 首先，收集一组围绕目标场景从多个角度拍摄的图片。这些图片应该覆盖场景的不同视角，以便捕捉到场景的全面信息。
    - 对每张图片，记录相应的相机参数，包括位置、方向和内部参数（如焦距和镜头畸变）。
2. 神经网络架构：  
    - NeRF 使用一个全连接的神经网络（通常没有卷积层）。这个网络接受一个 3D 空间点和一个视角方向作为输入，并输出该点的颜色和体积密度。
3. 渲染和训练过程：
    - 对于训练集中的每个像素，通过模拟从相机位置到该像素的光线来计算像素的颜色。具体来说，对每条光线在场景中的多个点进行采样，并通过神经网络预测这些点的颜色和密度。
    - 使用体积渲染技术，根据神经网络预测的颜色和密度，沿着光线积分以估计最终像素的颜色。
    - 使用渲染的像素颜色与实际图像中对应像素的颜色之间的差异来计算损失函数。这通常是一个均方误差损失。
4. 优化过程：
    - 使用梯度下降（通常是其变种，如 Adam 优化器）来优化神经网络的参数，以最小化损失函数。
    - 在整个训练集上重复这个过程，直到网络参数收敛。
5. 过拟合和正则化：
    - 由于训练数据可能有限，NeRF 模型可能面临过拟合的风险。为此，可能需要使用正则化技术（如权重衰减）。
    - 在某些情况下，也可以使用数据增强技术来增加训练数据的多样性。

# Ref

- NeRF Representing Scenes as Neural Radiance Fields for View Synthesis
- [知乎](https://zhuanlan.zhihu.com/p/631284285)
