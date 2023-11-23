---
share: true
---

# 卡方检验
## Concept
- 一种用来比较两个统计数据的检验技术；
- 当事实与期望不符合情况下使用卡方分布进行检验，看是否系统出了问题，还是属于正常波动；

## 卡方分布
- 描述k个独立正态分布的变量的平方和的分布：
	- k是其自由度；
	- Q = sum(Z_i^2), k个Z_i，Q ~ X^2(k)
- Usage:
	- 用来决定这两者之间是否有明显区别，或者说这些sample数据是不是来自同一个分布
	- 一种关于error的平方和的hypothesis test
- Ref
	- https://zhuanlan.zhihu.com/p/58556978
	- Wiki - Pearson's chi-squared test, Chi-squared distribution

# RANSAC
## Idea
- Seperate the outlier from the inlier
- 解决数据匹配的问题
	
## Approach
- Input
	1. data points：D
	2. 一个可以解释观测数据的参数化模型
	3. Hyperparameter
		- 模型需要多少点来做model fitting, s
		- 最大循环次数, k
		- fitness的阈值, t
		- inliner的个数阈值, n
- Process
	1. Sample the data points (s) from D to get the inliers
	2. Model Fitting using sampled inliers
	3. 计算剩余的点的fitness
	4. 把fitness大于t的点放入inliner，当局内点过少，废弃这轮获得的内点，跳回第一步
	5. repeat 1,2,3,4
	6. 当inlier超过n时即认为算法成功
- Output：最佳匹配的inlier和模型，或者失败
	
## 实验次数k的确定
- 输入
	1. Inlier Ratio, e
	2. Probability, p, 最终希望获得一个有用的模型的概率，也就是说这个模型本身不含任何outlier
	3. 模型需要多少点来做model fitting, s
- Approach
	1. 最终没有一个有用的模型的概率，1 - p；
	2. 在一次model fitting中全抽到inlier的概率，e^s；
	3. 一次model fitting失败的概率：即抽到的sample含有outlier的概率，1 - e^s
	4. k次model fitting失败的概率：(1 - e^s)^k，1 - p = (1 - e^s)^k
- Instance
	1. ORBSLAM：
		- 在计算匹配时使用了ransac
		- 并使用卡方验证判断model fitting是否成功，查看白化过后的特征点重投影误差的平方和是否超过阈值；
	2. VINS Mono：在计算基础矩阵时使用ransac，opencv的实现；
- Ref
	- https://blog.csdn.net/robinhjwy/article/details/79174914
	- https://en.wikipedia.org/wiki/Random_sample_consensus