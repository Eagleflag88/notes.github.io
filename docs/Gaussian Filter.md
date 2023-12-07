---
share: true
---
# Linear Kalman Filter
## Property
- Linearity assumption for the motion and meausrement equation;
- All states and measurements are reprensented by moments of gaussians;
- Update and Correction formulas in close-form;

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
	- 目标: 
		1. $x_{k+1}$的均值为0；
		2. $x_{k+1}$的方差最小；
- Process
	- 输入
		- $x_k$的均值和方差
		- $w_k$的均值和方差
		- $v_{k+1}$的均值和方差
	- Step 1 → 对eq1在$x_k$的均值处线性化
		- $x_{k+1} = f(mean(x_k)) + F(x_k - mean(x_k))$
		- $F$是$f$在$mean(x_k)$的偏导
	- Step 2 → 利用上式和输入推导$x_{k+1|k}$的均值和方差
	- Step 3 → 在$x_{k+1|k}$的均值处线性化eq2
		- $z_{k+1} = h(mean(x_{k+1|k})) + H(x_{k+1|k} - mean(x_{k+1|k}))$
		- $H$是$h$在$mean(x_{k+1|k})$的偏导
	- Step 4 → 令$x_{k+1|k+1} = x_{k+1|k} + K(z_{k+1} - Fx_{k+1|k+1})$，然后求$x_{k+1|k+1}$的均值和方差
	- Step 5 
		- 利用导数使$x_{k+1|k+1}$的方差最小
		- 获得$K$ → kalman增益
- Ref: 十四讲 eq 10.27

## Unscented Kalman Filter
- Idea
	1. Probes the function to be linearized at selected points and calculates a linearized approximation based on the outcomes of these probes.
	2. 相比EKF不需要线性化工作点
- Unscented Transform
	- 一种计算经过非线性系统传播过后的随机变量的统计量的方法 → $z = f(x)$
	- Process
		1. 计算$x$的sigma point → 根据$x$的均值和协方差生成一些sigma point
		2. 计算$z$的统计量 → 把$x$的sigma point放入$f$获得对应的$z$, 并计算$z$的均值和协方差
		3. Ref：The unscented kalman filter.pdf，eq 7.30 - 7.34
- Process
	- Prediction
		1. 根据$p(x_{k})$计算$x_{k}$的sigma point
		2. 对$p(x_{k+1}|x_k)$使用unscented transform，把$x_k$的sigma point转换为$x_{k+1|k}$的sigma point
	- Correction
		1. 基于$x_{k+1|k}$的sigma point通过likelihood function求得$z_{k+1}$的sigma point
		2. 计算kalman增益（根据EKF的结果），并获得最终$x_{k+1|k+1}$的均值和协方差
		3. Ref: The unscented kalman filter.pdf, eq 7.50 - 7.62

## Iterated Kalman Filter

- 迭代卡尔曼滤波器（Iterated Kalman Filter，IKF）是EKF的一个变体，用于非线性系统的状态估计。

- Process
	1. Prediction：与EKF一样，IKF的预测步骤使用系统的状态转移模型来预测下一个时间步的状态及其不确定性。
	2. Update：
	    - 在每个时间步，当新的观测数据到来时，IKF不是执行一次，而是多次执行更新步骤。
	    - 在每次迭代中，使用当前的状态估计来线性化非线性观测模型。
	    - 然后应用卡尔曼滤波器的更新公式来更新状态估计。
	    - 这个过程重复进行，直到状态估计收敛，即连续迭代间的变化小于某个阈值。
	3. 收敛判断：迭代继续进行，直到达到预定的迭代次数或状态估计的变化量小于设定的阈值。

- 优点
	1. 改善非线性处理：通过迭代更新，IKF能够更好地处理系统的非线性特性，提供比单次更新更精确的估计。
	2. 灵活性：可以根据系统的特性和观测数据的质量调整迭代次数。

- 缺点
	1. 计算成本：迭代过程增加了计算负担，特别是在迭代次数较多时。
	2. 收敛问题：在某些情况下，IKF可能不保证收敛，尤其是当非线性程度很高时。

# Information Filter
- Represent Gaussians in their canonical representation, information matrix and information vector, dual to moment representation as KF
- Estimation based on IF
	- dual to kalman filter
	- representing global uncertainty is simple in the information filter: simply set = 0
	- numerically more stable than the Kalman filter
	- More computational demanding than KF