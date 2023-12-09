---
share: true
---
# Preliminary
## 可观性
- 系统的可观性描述的是系统内部状态能够通过测量值观测到的可能性
- 线性系统的可观性
	- 线性系统: $x = Ax + Bu$, $z=Cx$
	- 通过可观性矩阵描述：
		- $O = [C; CA; CA^2; ..., CA^{n-1}]$ → n是状态的数量
		- 跟转移矩阵和观测矩阵相关
	- 当可观性矩阵满秩时，所有状态都可观
	- 系统中不可观的维度是可观性矩阵的零空间
- 非线性系统 → 通过李代数定义的可观性矩阵
- Ref：[Wiki](https://en.wikipedia.org/wiki/Observability)

## Consistency
- 当随着测量量增加，估计值会收敛至真实值，则是一致的
	
	
# EKF
## Issue
- 线性化之后的系统的可观性矩阵与transition matrix and observation matrix相关，当线性化的不一样时，系统的可观性会变化，这样就导致estimator的不一致性
- 在经典EKF中，转移矩阵的jacobian会通过在最新的状态估计值线性化计算 → 这会导致系统的可观性发生变化

## Solution
- FEJ-EKF
	- Pose使用上一轮完结之后的state来进行线性化，而不是本轮经过motion equation预测来的pose；
	- Landmark使用第一次线性化时的state；
- Observability-Constraint EKF → 在imu covariance propagation的transition中，保持null space，而不是修改线性化的点

## Ref
- https://docs.openvins.com/fej.html
- A First-Estimates Jacobian EKF for Improving SLAM Consistency.pdf
- Camera-IMU-based localization: Observability analysis and consistency improvement.pdf 6.1

# Sliding window based SLAM
## Issue
- 当新加入的观测关系跟原来的状态有关系时，在优化完成之后，会导致同一个优化变量在不同轮的线性化点不一样
- 窗口内的变量的信息矩阵会因为同一个变量在不同的点被线性化而增加Rank
- Ref
	1. Consistency Analysis for Sliding-Window Visual Odometry.pdf eq 19 and section IV
	2. vio_course 第四讲 p36，状态2的线性化点在加入新观察点后就不一致了

## Solution
- First Estimate Jacobian (FEJ) → 对所有跟被marg的变量有连接的remaining的变量，在计算residual jacobian时使用第一次优化的结果；注意仅仅是计算jacobian时固定，优化还是可以被优化的