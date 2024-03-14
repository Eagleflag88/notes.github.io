---
share: true
---

# Multi View Reconstruction
- 3D Reconstruction through multiple (N>2) images
- Ego-Pose and Mapping
- Feature Based Approach
- Sparse Reconstruction

# Idea
- Start with a initial guess of the 3d points and the feature point match
- Minimize the reprojection error to get the camera pose and 3d points
- Data Association between 3d points and features is an import aspect

# Problem

## 观测方程
- $Z = f(X)$：$P_{uv} = KTP_w$
- 3D空间点投影到图像像素点
- $P_{uv}$: 特征点的像素坐标 → 2维

## 优化参数, X
- Camera Pose 6, $T$
- Map Point 3, $P_w$

## 目标
- Maximum Likelihood
	- $L = (Z - f(X))^T\Omega^{-1}(Z - f(X))$
	- $\Omega$是协方差矩阵
- $\min L → X$
- 信息矩阵是亏秩的，因为系统有不可观的自由度:
	- 单目SLAM
		- 绝对尺度不可观 → 1维
		- 绝对位姿不可观，只能得到相对于某个固定pose的位姿 → 6维
	- VIO
		- 绝对位姿部分不可观 → 4维
		- pitch和roll由重力方向可观
		- 尺度由加速度获得

# Approach
- 根据上面的观测方程构建最小二乘问题，利用LM算法求解；
- 最后的核心是增量方程$J^T\Omega^{-1}J\Delta X = -J(Z - f(X))$ → 求解$\Delta X$

## Process
- Step 1: 建立Jacobian Matrix，$J$
	- row → 观测数x2
	- col → pose数量x6 + 路点数量x3
- Step 2：根据每个测量点关联的优化变量把jacobian填入$J$中，每一行包含
	- 2x6 Block for pose
	- 2x3 Block for 3d路点
- Step 3：求信息矩阵$H$
	- $H = J^T\Omega^{-1}J$
	- $\Omega$误差的协方差矩阵
- Step 4: 利用$H$的稀疏性求解增量方程 → 参见[[Marginalization#Application|Marginalization > Application]]的BA部分；

## Ref
- Bundle adjustment gone public.pdf, p11
- 第4节：滑动窗口理论.pdf, p30

# 核函数
- Idea
	- 使损失函数在误差$e$较大的时候增长减慢
	- 把二范数度量换成增长不那么快的函数
- Variante
	- Huber核: 超过一定阈值后变为线性增长
	- Cauchy核: $\log(1 + e^2)$
- 使用95%有效性来选择核函数中的参数
- Ref: 第3节：基于优化的IMU预积分与视觉信息融合.pdf

# Initial Guesses (Frontend)
## Idea
- Compute relative transform through first image pair
- Compute subsequent transform and 3d point using pnp
- Con: may lose track during the process because practical problems: strong motion blur or something else

## Dealing with Outliers
- Wrong association (最大的原因)
	- We need at least 4 Observation (5 -6 with high quality) to identify the wrong association
	- 2个Observation：只能得到交点
	- 3个Observation：当3条射线交点差别很大时 (Using Statistical Test)，应该有outlier
- Approaches
	- 在小范围内（5-6 images）进行outlier detection，剔除outlier之后进行整体BA
	- Only use features observed by a sequence of 3-6 images 
	- Using RANSAC
	- Use robust kernel to reduce the penalty for big errors, actually lead to a weighted LS problem