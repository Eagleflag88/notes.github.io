---
share: true
---
# Problem

- 之前的稀疏的检测模型容易有训练不收敛的问题，而这个问题主要是因为一对一的匹配；


# Approach

- 使用Denoising task as auxiliary supervision；
	- 在初始化阶段我们初始化一些可学习的instance，同时我们会在GT的基础上掺杂一些噪声，初始化成instance；
	- 我们也会接受来自上一个时刻的instance；
- Introduce task of quality estimation as auxiliary supervision；
	- 对每个target我们预测他的centerness和yawness来评估pred的quality；
- 使用decoupled attention，而不是vanilla attention。核心想法是postional embedding和Query不要相加，而是concat；
- 尾部接续tracking模块。直接输出tracking结果：
	- 实质上就是ID assignment；


# Architecture

- As [[2023-Sparse4d v2#Architecture|2023-Sparse4d v2 > Architecture]]；

# Training

- Sequential iteration approach for training
- Temporal instance denoising
- Quality estimation tasks
- Dense depth regression as in [[2023-Sparse4d v2#Dense Depth Supervision|2023-Sparse4d v2 > Dense Depth Supervision]]


# Code

- https://github.com/HorizonRobotics/Sparse4D.git
