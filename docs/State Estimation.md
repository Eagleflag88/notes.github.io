---
share: true
---
# Problem Formulation

## Hidden Markov model
- Initial density for state；
- Motion transition → State at time $k$ is stochastically dependent on the state at time $k-1$ and the control $u$
	- 通过全概率定理和$p(x_k|x_{k-1})$的条件概率通过积分可获得$p(x_k)$
	- $p(x_k) = \int p(x_k|x_{k-1})p(x_{k-1}|z_{k-1})dx$
- Measurement → The measurement $z_k$ depends stochastically on the state at time $k$
	- 通过bayesian rule，测量值的likelihood function获得归一化的后验概率分布
	- $p(x_k|z_k) = \frac{p(z_k|x_k)p(x_k)}{p(z_k)}$
## Belief
- represented by conditional probability distributions
- assigns a probability (or density value) to each possible hypothesis with regards to the true state
- posterior probabilities over state variables($x_k$) conditioned on the available $data(z_{1:k})$ →$bel_k = p(x_k|z_{1:k})$

# Approach
## Maximum Likelihood
- Actually a frequntic approach
- The state is taken as constant not a distribution
- No Prior can be included
- Covariance Matrix

## Bayesian Approach
- Bayesian Law for state estimation $p(x|z)$ → 最小化Risk Function
	- $R = \int L(x, x')p(x|z)dx$
	- $L(x, x')$是损失函数
- Loss function
	- quadratic
		- $L(x, x') = (x - x')^2$
		- Solution → $E(p(x|z))$
	- 只有刚好符合才等于1
		- 意义为最大可能出现的状态
		- Solution: mode of the distribution(MAP) → $\max(p(x|z))$
	- 绝对值: $L(x, x') = |x - x'|$ →Solution: $median(p(x|z))$
	- Ref: Diss_Xie.pdf, p25
- Recursive Bayesian Filter
	- Idea
		- Calculate the belief in a recursive way
		- find relation between $bel_k$ and $bel_{k-1}$
	- Derivation
		- $p(x_k|z_{1:k}) = \frac{p(z_{1:k}|x_k)p(x_k)}{p(z_{1:k})}$
		- $p(x_k|z_k)$和$p(x_{1:k}|z_{1:k})$做为推到起始点并不合适
		- $p(z_{1:k}|x_k) = p(z_k|x_k)$
			1. $z_k$仅跟$x_k$有关，$z_{1:k-1}$跟$x_k$无关
			2. 获得$p(x_k|z_{1:k})=\frac{p(z_k|x_k)p(x_k)}{p(z_{1:k})}$
			3. Correction
		- Prediction →$p(x_k) = \int p(x_k|x_{k-1})p(x_{k-1}|z_{1:k-1})dx$
		- 利用$bel$的定义可以看出整个estimator是使用递归的运行方式
			- $bel_k = f(bel_{k-1}, z_k)$
			- 实际上通过递归的方式获得了整个轨迹的估计值
	- Ref
		- Probabilistic Robotics, ch 2.4.3
		- https://en.wikipedia.org/wiki/Recursive_Bayesian_estimation
		- https://zhuanlan.zhihu.com/p/133862177 → 说明递归的意义
	- 只有Filtering，之前的所有状态已经边缘化掉了，无法再被线性化获得信息