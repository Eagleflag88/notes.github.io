---
share: true
---

# Ref
Original Paper: Deformable Convolutional Networks

# Problem
- 固定的卷积组件无法适应图片中多变和不规则的几何变换；
	1. A convolution unit samples the input feature map at fixed locations;
	2.  a pooling layer reduces the spatial resolution at a fixed ratio; 

# Approach

- Deformable convolution：
	1. 偏移量学习：首先，网络会学习一个偏移量对于每个卷积核位置。这些偏移量是实数，可以是正值或负值，表示每个卷积点应该在空间上移动的距离；
	2. 采样点更新：标准卷积的采样点位置固定不变，而Deformable Convolution根据学习到的偏移量动态调整采样点的位置；
	3. 双线性插值：因为偏移量可能是小数，直接使用会导致采样点落在像素格点之间，所以需要用双线性插值来计算这些位置的像素值；
	4. 加权求和：最后，与标准卷积类似，使用更新后的采样点的像素值与卷积核的权重进行加权求和，以产生最终的输出；

# Variant

Deformable convolution V2:
- Modulated Deformable Convolutions: DCNv2 introduces modulated deformable convolutions that add an additional learnable parameter to control the contribution of each spatial sample point.
- Learning to Deform: DCNv2 improves the mechanism through which the offsets are learned. The offsets are learned in a more effective manner, allowing the network to adapt the convolutional kernels more dynamically to the input features.