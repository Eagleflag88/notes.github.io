---
share: true
---

# Idea
- 在对特征点进行初始化之后，不再把他们放进优化问题进行考虑;
- 只考虑pose本身 → 减少计算量，使SLAM问题能够real-time运行

# Structure
- Node: 仅仅是Pose → state, $x$
- Edge
	- Constraint: 	$z$ → real observation
	- Observation or odometry measurements
		- 2D: Image Registration
		- 3D: Point Cloud Registration
	- Should be specified with uncertainty

 # Problem
 - The system is described by observation function $z = f(x)$，描述的一般是两个pose之间的变化量；
 - Residual: 
	 - $z - f(x)$
	 - variance: $\sigma$
- Loss function
	- $L = (Z - f(X))^T\Omega^{-1}(Z - f(X))$
	- $\Omega$: 残差的协方差矩阵

# Approach
- Frontend: Generate Edge through data association
- Backend: Maximum Likelihood
	- 测量值: $z_{k+1}$, 两个结点之间的位姿变化值 → 通过配准方法获得
	- 估计值: $f(x_k, x_{k+1}) = x_{k+1} - x_k$
	- 残差: $z_{k+1} - (x_{k+1} - x_k)$
- Relative Uncertainty: Through marginalization
- Property
	- Sparsity: 
		- 测量值只和相关的节点有关，增量方程的信息矩阵有其稀疏性
		- 利用Sparse Linear Sovler来求解增量方程
			1. CSparse Package
			2. Logical ordering 
			3. Approximate minimal degree
- Rank Deficiency
	- 原因: 因为pose graph描述的是相对constraint，导致系统有不可观性;
	- 措施
		- 对pose添加一个prior
		- Fixing a subset of the variables
			- Make it disappear from the linear system
			- Conditioning on the fixed variables
		- Damping of the information matrix → LM算法
- Ref：Graph-Based SLAM A Least Squares Approach to  SLAM using Pose Graphs
