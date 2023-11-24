---
share: true
---
# Multivariate Gaussian Distribution
- $p(X) = \exp[-0.5*(X - \mu)^T \Sigma^{-1}(X, X)(X - \mu)]$
- $X$：多元变量
- 在SLAM中很多时候应该处理成0均值的变量
- $\mu$：均值向量
- $\Sigma(X, X)$：协方差矩阵
- 信息矩阵，$\Lambda = \Sigma^{-1}$ → 信息矩阵的元素如果为零表明其代表的变量不相关

# 舒尔补
## Idea
- 考虑任意方阵
 $$ 
 M=\begin{bmatrix} 
   A & B  \\
   C & D
 \end{bmatrix}
 $$
- 通过定义舒尔补可以快速求得$M$的逆阵
- 条件是$A$和$D$矩阵可逆

## Derivation
- 考虑线性系统
 $$ 
 M\begin{bmatrix} 
   x  \\
   y
 \end{bmatrix}
 =\begin{bmatrix} 
   c  \\
   d
 \end{bmatrix}
 $$
- 利用消元法求解线性系统，得到$x$和$y$的表达式
	- $x = (A - BD^{-1}C)^{-1}c - (A - BD^{-1}C)^{-1}BD^{-1}d$
	- $y = -D^{-1}C(A - BD^{-1}C)^{-1}c + (D^{-1} + D^{-1}C(A - BD^{-1}C)^{-1}BD^{-1})d$
- 观察上式可获得$M^{-1}$，因为
$$ 
 \begin{bmatrix} 
   x  \\
   y
 \end{bmatrix}
 =M^{-1}\begin{bmatrix} 
   c  \\
   d
 \end{bmatrix}
 $$
- Ref: The Schur Complement and Symmetric Positive Semidefinite (and Definite) Matrices

## Property
- 实际上就是Variable elimination in a probabilisitic view
- 消元之后，被边缘化的结点包含的信息被转移到剩余的结点中，总体的信息量保持不变
- 但是消元之后，当有新的跟老的状态相关的测量值时，已经被丢弃的老的状态无法更新
- 经过marginalization之后，保留的状态的信息矩阵会出现变化

# Application
## 滑窗管理
- Problem
	- 滑窗中$a$和$b$分别是需要被marg和留下的状态
	- $p(a, b)$的协方差矩阵
	$$ 
 M=\begin{bmatrix} 
   A & B  \\
   C & D
 \end{bmatrix}
 $$
	- 其中$A = \Sigma(a, a)$, $D = \Sigma(b, b)$
	- 目标
		- 条件概率：$p(b|a)$ → 即边缘化后的概率分布
		- 边际概率：$p(a)$ → 副产品
- Usage
	- 从协方差矩阵M中获得概率分布
		- $p(a)$的协方差矩阵是$A$
		- $p(b|a)$的协方差矩阵是$A$的舒尔补 → $D - CA^{-1}B$
		- Ref：vio_course 第四节 eq32
	- 从信息矩阵inv(M)中获得概率分布 
$$ 
 M^{-1}=\begin{bmatrix} 
   \Lambda_{aa} & \Lambda_{ab}  \\
   \Lambda_{ab} & \Lambda_{bb}
 \end{bmatrix}
 $$
可以通过舒尔补快速获得
            - $p(a)$的协方差矩阵是$A = \Lambda_{aa} - \Lambda_{ab}\Lambda_{bb}^{-1}\Lambda_{ba}$
            - $p(b|a)$的信息矩阵是$\Lambda_{aa}$ → 我们想要的
            - Ref：vio_course 第四节 eq 37，38
			 - 同时会让BA的normal matrix变得更稠密

## BA
- Problem：求解$H\Delta X = G$
	- $H$: Normal Matrix：$H = [B \ E; E^T \ C]$
		- $B$为位姿矩阵
		- $C$为路标
		- $B$比$C$的维数小很多
	- $\Delta X = [\Delta c; \Delta p]$
	- $G = [v; w]$
- Approach
	- Idea：求解reduced linear system, 减少计算量
		- pose的数量比feature数量少很多
		- Reduced linear system就是关于pose的
	- Process
		- 求$C$的逆阵$C^{-1}$
		- 利用舒尔补获得关于位姿的增量方程
			1. $[B - EC^{-1}E^T]\Delta c = v - EC^{-1}w$，reduce camera system。$S = [B - EC^{-1}E^T]$ → 一个半正定矩阵
			2. 问题规模与$B$一样，规模不大
			3. 根据$S$的稀疏性，ceres求解器会选择dense/sparse solver
				- Sparse cholesky decomposition
				- Preconditioned conjugate gradient
			4. Ref：十四讲 eq 10.59
		- 得关于路标的增量方程 → $\Delta p = C^{-1}(w - E^T\Delta c)$
## Null-space operation
- 对需要被边缘化的变量的residual jacobian利用其null space vector来消除，MSCKF
- Ref: Null-Space-based Marginalization Analysis and Algorithm