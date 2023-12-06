---
share: true
---
# Linear Case
## Ordinary
- Problem
	- $Y = \beta X + \epsilon$
		- $Y = [y_1, y_2, ..., y_m]^T$, mx1
		- $\beta = [\beta_1, \beta_2, ..., \beta_n]^T$, nx1
		- $X$, mxn
		- $\epsilon$, mx1
			- uncorrelated independent
			- normally distributed
	- $L =  (Y - X\beta)^T(Y - X\beta)$
		- 表示误差的平方和
		- find $\beta$ which minimize $L$
- Solution
	- 通过对$L$函数求$\beta$向量的偏导
	- $XX^T\beta = X^TY$ → normal equation
	- Ref: Least squares in matrix form，eq3.6

## Weighted
- Problem
	- $Y = \beta X + \epsilon$ → $\epsilon$, mx1
		- Variance is dependent of the measurement
		- Uncorrelated Variance
	- $L = (Y - X \beta)^TW(Y - X \beta)$
		- Weighted Square Sum
		- 其中Covariance Matrix: $W = diag(1/\epsilon^2)$
		- $W$的非对角线元素为0
- Solution
	- 在$W$已知的情况下，通过对$L$函数求$\beta$向量的偏导
	- $XWX^T \beta = X^TWY$ → weighted normal equation
	- Ref: [wiki](https://en.wikipedia.org/wiki/Weighted_least_squares)

## Generalized
- Problem: 
	- $Y = \beta X + \epsilon$ → $\epsilon$, mx1
		- Variance is dependent of the measurement
		- Correlated Variance
	- $L = (Y - X \beta)^TW(Y - X \beta)$
		- Square Mahalanobis Sum of Residual Vector
		- 其中$W$是Information Matrix，是协方差矩阵的逆
		- $W$的非对角线元素不为0；
- Solution
	- 在$W$已知的情况下，通过对$L$函数求$\beta$向量的偏导
	- $XWX^T \beta = XWY$
	- Ref：[wiki](https://en.wikipedia.org/wiki/Generalized_least_squares#Feasible_generalized_least_squares)

# Nonlinear Case
## Problem

-  一般形式 → $Y = f(X, \beta) + \epsilon$
- $L =  (Y - f(X, \beta))^T(Y - f(X, \beta))$
	- 表示误差的平方和
	- find $\beta$ which minimize $L$

## Solution
- General Process
	1. 把目标函数在$X$处关于$\beta$进行泰勒展开： $Y = f(X, \beta + \Delta \beta) =f(X, \beta) + \frac{\partial f}{\partial\beta}\Delta \beta$，把问题转化为线性问题；
	2. 求解关于$\beta$的线性问题；
- Gauss-Newton
	- 属于迭代法
		- 且专门用来解最小二乘法
		- 先找到迭代方向，再求得步长
	- Process
		1. 每一次迭代，定义损失函数：$L = (Y - f(X, \beta + \Delta \beta))^T(Y - f(X, \beta + \Delta \beta))$；
		2. 对$f(X, \beta + \Delta \beta)$进行泰勒展开，然后对$L$求导，得到关于$\Delta \beta$的normal equation，即所谓增量方程 → $J^TJ \Delta \beta = -J^T\Delta Y$
		3. $J^TJ$矩阵有可能比较病态，$\Delta \beta$的解数值上可能会不稳定
	- Ref：十四讲，eq 6.20

- Levenberg-Marquadt
	- 对$\Delta \beta$添加一个信任区域
		- 判断标准：$\rho = [f(X, \beta + \Delta \beta) - f(X, \beta)]$的精确值和近似值之比
		- 当发现不在信任区域之内时，则缩小信任区域半径
	- Process
		- 先确定信任区域，然后求信任区域之内的最优点（包括方向和步长）
		- 在一定半径内最小化损失函数
			- $L = (Y - f(X, \beta + \Delta \beta))^T(Y - f(X, \beta + \Delta \beta))$
			- $D \Delta \beta < \lambda$
		- 转化为无限制的优化问题
			- $min [L + \lambda(D\Delta \beta)^2]$ →泰勒一阶近似
			- $\lambda$为阻尼因子
				- $\lambda$很大 → 趋近于最速下降法
				- $\lambda$很小 → 趋近于GN法
		- 对$\Delta \beta$求导并获得增量方程
			- $[J^TJ + \lambda D^TD]\Delta \beta = -J^T\Delta Y$
			- $\lambda$能使增量矩阵保持正定，使迭代方向一直朝下
		- $\lambda$更新策略
			- $\lambda$名为阻尼因子，实际上隐式地包含了信任区域
			- Marquardt方法
				- 当$\rho>3/4$: 近似良好，应该扩大信任范围 → $\lambda = 2\lambda$
				- 当$\rho<1/4$: 应该缩小信任范围 → $\lambda = \lambda/3$
				- 在他们之间时 → 顺利迭代
			- Nielson方法 → 多引入一个控制参数
	- Variant: 处理Scaling的问题 → $D = diag(J^TJ)$
	- Ref
		1. 十四讲，eq 6.24
		2. https://zhuanlan.zhihu.com/p/136143299