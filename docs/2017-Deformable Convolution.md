---
share: true
---

# Problem
- 固定的卷积组件无法适应图片中多变和不规则的几何变换；
	1. A convolution unit samples the input feature map at fixed locations;
	2. A pooling layer reduces the spatial resolution at a fixed ratio; 

# Approach

- Deformable convolution：
	1. 偏移量学习：首先，网络会学习一个相对于每个卷积核位置的偏移量。这些偏移量是通过一个小的CNN来学习出来的。这些偏移量是实数，可以是正值或负值，表示每个卷积点应该在空间上移动的距离；
	2. 采样点更新：标准卷积的采样点位置固定不变，而Deformable Convolution根据学习到的偏移量动态调整采样点的位置；
	3. 双线性插值：因为偏移量可能是小数，直接使用会导致采样点落在像素格点之间，所以需要用双线性插值来计算这些位置的像素值；
	4. 加权求和：最后，与标准卷积类似，使用更新后的采样点的像素值与卷积核的权重进行加权求和[[CNN Basics#Standard Convolution|CNN Basics > Standard Convolution]]，以产生最终的输出；
- Termed as DCNv1;

# DCNv2

## Problem

- DCNv1生成的spatial support很可能扩展到图片上的背景部分，让卷积操作被无关部分影响；

## Approach

- Modulated Deformable Convolutions: DCNv2 introduces modulated deformable convolutions that add an additional learnable parameter to control the contribution of each spatial sample point.
- Learning to Deform: DCNv2 improves the mechanism through which the offsets are learned. 通过knowledge distillation的框架我们把R-CNN当作teacher让DCNv2学到更好的特征；

# Ref
- Deformable Convolutional Networks
- Deformable ConvNets v2: More Deformable, Better Results