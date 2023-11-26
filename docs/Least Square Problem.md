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
	- 在W已知的情况下，通过对L函数求\beta向量的偏导
	- $XWX^T \beta = XWY$
	- Ref：[wiki](https://en.wikipedia.org/wiki/Generalized_least_squares#Feasible_generalized_least_squares)

# Nonlinear Case
## Problem
- 一般形式 → $Y = (X, \beta) + \epsilon$
- 优化问题中的形式 → $Y = f(X + \Delta X, \beta) + \epsilon$

## Solution
- Gauss-Newton
	- 属于迭代法
		- 且专门用来解最小二乘法
		- 先找到迭代方向，再求得步长
	- 每一次迭代，定义损失函数：$L = (Y - f(X + \Delta X))^T(Y - f(X + \Delta X))$
		- 求令$L$最小的$\Delta X$
		- $f(X + \Delta X)$可以利用泰勒展开近似
	- 对$f(X + \Delta X)$进行泰勒展开，然后对$L$求导，得到增量方程 → $J^TJ \Delta X = -J^Tf(X)$
	- $J^TJ$矩阵有可能比较病态，$\Delta X$的解数值上可能会不稳定
	- Ref：十四讲，eq 6.20
	- Variant → 在损失函数中增加超参数$\alpha$，$L = (Y - f(X + \alpha\Delta X))^T(Y - f(X + \alpha\Delta X))$

- Levenberg-Marquadt
	- 对$\Delta X$添加一个信任区域
		- 判断标准：$\rho = [f(X + \Delta X) - f(X)]$的精确值和近似值之比
		- 当发现不在信任区域之内时，则缩小信任区域半径
	- Process
		- 先确定信任区域，然后求信任区域之内的最优点（包括方向和步长）
		- 在一定半径内最小化损失函数
			- $L = (Y - f(X + \Delta X))^T(Y - f(X + \Delta X))$
			- $D \Delta X < \lambda$
		- 转化为无限制的优化问题
			- $min [L + \lambda(D\Delta X)^2]$ →泰勒一阶近似
			- $\lambda$为阻尼因子
				- $\lambda$很大 → 趋近于最速下降法
				- $\lambda$很小 → 趋近于GN法
		- 对$\Delta X$求导并获得增量方程
			- $[J^TJ + \lambda D^TD]\Delta X = -J^Tf(X)$
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
- Dog Leg
	- Idea
		1. 显性的使用trust region的概念
		2. 结合GN和Steep Descent(SD)
		3. 在Trust Region中，目标函数被利用泰勒展开所近似
	- Process
		1. 在确定的Trust Region中
		2. 首先尝试用GN来获得增量
			- 一般认为GN的解比SD的解大
			- 当增量在Trust Region之内时，则使用GN的增量
			- 当增量超出Trust Region，则使用Steep Descent来求解
		3. 然后使用SD来求解
			- 单纯用SD求出来的增量为Cauchy Point
			- 当Cauchy Point在Trust Region之内，则接受
			- 当Cauchy Point在Trust Region之外，则使用Cauchy Point和GN解组成的直线和trust region边缘的相交点作为下一个迭代点
	- Trust Region更新策略：跟Marquadt方法类似
	- Performance：LM每次失败之后需要修改damping factor之后重新求解 → 理论上DL会更快
	- Ref
		1. Dog Leg Wiki
		2. Is Levenberg-Marquardt the Most Efficient Optimization Algorithm for Implementing Bundle Adjustment?.pdf