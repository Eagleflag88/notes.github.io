---
share: true
---
# Idea
- Do not rely on a fixed functional form of the posterior
- Adaptive to the complexity of the posterior

# Approach

## Histogram filter/Discrete bayes filter
- Concept
	1. piecewise constant approximations to a continuous density
	2. decompose the state space into finitely many regions with cumulative probability
- Process → piecewise approximation of the bayesian prediction and update rule
- Region Decomposition
	- static grid
	- density tree based → The less likely a region, the coarser the decomposition
- Selective updating → only update the grid whose posterior density is bigger than threshold

## Particle filter
- Concept
	- 通过在后验概率采样来获得采样点
	- 采样点的集合被用来近似后验概率本身
- Problem
	- Set of Particles → $S_k$, Number of Particles $M$
	- 求$p(x_k|z_{0:k})$ → 由采样点$S_k$所近似
- Process
	- Input
		1. 上一个时刻的采样点集$S_{k-1}$
		2. 最新的测量值$z_k$
	- Step 1
		- 从$p(x_k|x_{k-1})$中采样，为$S_{k-1}$的每一个点生成采样点集$S_{k|k-1}$ → 实际上就是根据动力方程计算
		- 这个点集代表的是经过prediction的概率分布，$p(x_k)$
	- Step 2
		- 为$S_{k|k-1}$中的每一个点计算importance factor，$w_{k,i}$
		- 实际上importance factor就是每个点的likelihood，用以把测量值的信息融入后验概率 → $w_{k,i} = p(z_k|x_{k,i})$
	- Step 3 → Importance Resampling
		- 从$p(x_k)$中重采样$M$个sample
			- 每个particle被采到的概率跟importance相关；
			- 使得高权重的粒子更有可能被选择，低权重的粒子被淘汰，从而保持样本的多样性和避免样本退化；
			- 这个过程中不会直接更新粒子的权重，而是通过改变同一权重的粒子的数量改变整个权重在粒子之中的分布；
			- 最终所有的粒子的权重一样；
	- Step 4 -> 根据重采样出来的粒子更新状态
		- 计算所有粒子的加权平均来获得$p(x_k|z_k)$
		- Alternative: 不进行resampling而是直接利用likelihood更新权重 → $w_{k,i} = p(z_k|x_{k,i})w_{k-1,i}$
		- Ref: Probabilistic Robotics.pdf，p79
- Importance Resampling
	- Problem → 从任意概率密度函数$f$中获取采样点
		- $f$此时是未知的
		- 我们只能计算$\frac{f(x)}{g(x)}$
	- Approach
		- 当我们知道每个sample的权重时，我们可以获得符合$f$的概率分布
		- Input
			1. target distribution → $f$
			2. proposal distribution → $g$
				- 应用条件：当$f$ > 0时，$g$ > 0 → 我们能$g$中直接生成样本
		- Process
			1. 从$g$中采样：	
				- 获得采样点集，$S_g$；
				- pdf描述的是随机变量X分布在这个位置的概率 → $pdf = g(x)$；
				- 每个采样点由其位置和pdf的值组成 →  $s = [x, pdf]$；
			3. 计算点集的权重: $w_i = \frac{f(s_i)}{g(s_i)}$ → 其实每个采样点的$x$是没变的，改变的只是他的pdf的值
			4. 利用权重更新$S_f = WS_g$
			5. $S_f$即可以认为是从f中采样出来的点集 →这样可以获得$f$的分布
	- Particle Filter
		- 经过prediction之后的$p(x_k)$为proposal分布
		- $p(x_k|z_k)$为target分布
		- $p(z_k|x_k)$是采样点的权重 → $p(z_k|x_k) = \frac{p(x_k|z_k)}{p(x_k)}$
	- Ref: Probabilistic Robotics.pdf，figure 4.2
	- Best Practice → 粒子数量
		- 一直采样直到新的测量值到来
		- 获得一种resource-adaptive的实现