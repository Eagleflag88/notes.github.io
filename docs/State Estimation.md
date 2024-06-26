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

## Likelihood Function
- 似然函数（Likelihood Function）: 定义为给定参数$\theta$下，观测到数据$x$的概率；
- 对随机变量$x$来说，它的似然函数可以认为是它的固定参数下的密度函数；
$$
L(\theta | x) = f_{\theta}(x)
$$

## Maximum Likelihood Estimation

- 它基于一个假设，即当从模型总体随机抽取$n$组样本观测值后$\textbf{x}=[x_0, x_1, ..., x_n]$，最合理的参数估计量应该使得从模型中抽取该$n$组样本观测值的概率最大：
$$
\hat{\theta}=arg max \prod_0^n L_n(\theta, \textbf{x})
$$
- 是一种的点估计法，跟Uniform Prior的MAP估计等价；
- 一般通过最小化Likelihood的对数来计算求解；
- No Prior can be included；

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

## Recursive Bayesian Filter
- Idea
		- Calculate the belief in a recursive way
		- find relation between $bel_k$ and $bel_{k-1}$
		- $p(z_{1:k}|x_k) = p(z_k|x_k)$，$z_k$仅跟$x_k$有关，$z_{1:k-1}$跟$x_k$无关
- Process
	1. Prediction →$p(x_k) = \int p(x_k|x_{k-1})p(x_{k-1}|z_{1:k-1})dx$
	2. Correction → $p(x_k|z_{1:k})=\frac{p(z_k|x_k)p(x_k)}{p(z_{1:k})}$，这个时候只是获得了后验概率，还需要使用相应的损失函数来获得点估计；
- Recursion
	- 利用$bel$的定义可以看出整个estimator是使用递归的运行方式
	- $bel_k = f(bel_{k-1}, z_k)$
	- 实际上通过递归的方式获得了整个轨迹的估计值
- Ref
		- Probabilistic Robotics, ch 2.4.3
		- https://en.wikipedia.org/wiki/Recursive_Bayesian_estimation
		- https://zhuanlan.zhihu.com/p/133862177 → 说明递归的意义
		- Introduction to recursive Bayesian filtering
		