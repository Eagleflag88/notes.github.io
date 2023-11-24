---
share: true
---
# Linear Kalman Filter
## Property
- Linearity assumption for the motion and meausrement equation
- All states and measurements are reprensented by moments of gaussians
- Update and Correction formulas in close-form

## Derivation
- Ref：十四讲 eq 10.9

# Nonlinear Kalman Filter
		
## Extended Kalman Filter
- state probability and the measurement probabilities are governed by nonlinear functions
- the Bayes filter does not possess a closed-form solution.
- overcomes the linearity assumption by linearization via Taylor approximation
- Are incapable of representing such multimodal beliefs but can be extended with mixture gaussian representation → multi-hypothesis (extended) Kalman filters

## Derivation
- Problem
	- State space model:
		- $x_{k+1} = f(x_k)+ w_k$， eq1
		- $z_{k+1} = h(x_{k+1}) + v_{k+1}$， eq2
	- 目标: 错误$x_{k+1}$的均值为0矩阵的方差最小
- Process
	- 输入
		- $x_k$的均值和方差
		- $w_k$的均值和方差
		- $v_{k+1}$的均值和方差
	- Step 1 → 对eq1在$x_k$的均值处线性化
		- $x_{k+1} = f(mean(x_k)) + F(x_k - mean(x_k))$
		- F是f在$mean(x_k)$的偏导
	- Step 2 → 利用上式和输入推导$x_{k+1|k}$的均值和方差
	- Step 3 → 在$x_{k+1|k}$的均值处线性化eq2
		- $z_{k+1} = h(mean(x_{k+1|k})) + H(x_{k+1|k} - mean(x_{k+1|k}))$
		- H是h在$mean(x_{k+1|k})$的偏导
	- Step 4 → 令$x_{k+1|k+1} = x_{k+1|k} + K(z_{k+1} - Fx_{k+1|k+1})$，然后求$x_{k+1|k+1}$的均值和方差
	- Step 5 
		- 利用导数使$x_{k+1|k+1}$的方差最小
		- 获得$K$ → kalman增益
- Ref: 十四讲 eq 10.27

## Unscented Kalman Filter
- Idea
	1. probes the function to be linearized at selected points and calculates a linearized approximation based on the outcomes of these probes.
	2. 相比EKF不需要线性化工作点
- Unscented Transform
	- 一种计算经过非线性系统传播过后的随机变量的统计量的方法 → y = f(x)
	- Process
		1. 计算x的sigma point → 根据x的均值和协方差生成一些sigma point
		2. 计算y的统计量 → 把x的sigma point放入f获得对应的y, 并计算y的均值和协方差
		3. Ref：The unscented kalman filter.pdf，eq 7.30 - 7.34
- Process
	- Prediction
		1. 根据$p(x_{k-1})$计算$x_{k-1}$的sigma point
		2. 对$p(x_t|x_{t-1})$使用unscented transform，把$x_{k-1}$的sigma point转换为$x_{k|k-1}$的sigma point
	- Correction
		1. 基于$x_{t|t-1}$的sigma point通过likelihood function求得$z_t$的sigma point
		2. 计算kalman增益，并获得最终$x_{t|t}$的均值和协方差
		3. Ref: The unscented kalman filter.pdf, eq 7.50 - 7.62

# Information Filter
- Represent Gaussians in their canonical representation, information matrix and information vector, dual to moment representation as KF
- Estimation based on IF
	- dual to kalman filter
	- representing global uncertainty is simple in the information filter: simply set = 0
	- numerically more stable than the Kalman filter
	- More computational demanding than KF