---
share: true
---

# 卡方检验
## Concept
- 这个检验用来确定一组观测数据是否符合预期数据；
- 当观测与期望不符合情况下使用卡方分布进行检验，看是否系统出了问题，还是属于正常波动；

## 卡方分布
- 描述$k$个独立正态分布的变量$Z_1,Z_2,Z_3,...,Z_k$的平方和的分布：
	- $k$是其自由度；
	- $Q = \sum(Z_i^2)$, $k$个$Z_i$，$Q \sim \chi^2(k)$

- 卡方分布的特点包括：
	1. 非负性：由于卡方分布是方差的和，它的值始终是非负的；
	2. 形状：卡方分布是偏斜的，随着自由度的增加，其形状逐渐接近正态分布；
	3. 自由度：自由度是卡方分布的参数，通常用$k$表示。自由度等于组成卡方统计量的独立正态分布随机变量的数量；

## Process

1. 建立假设:
    - 零假设$H_0$​: 理论分布与观察分布相符；
    - 对立假设$H_1$: 理论分布与观察分布不相符；
2. 计算卡方统计量:
    - 计算观测值$O_i$与期望值$E_i$之间的差异；
    - 计算卡方统计量$\chi^2$的公式为: $\chi^2=\sum \frac{(O_i - E_i)^2}{E_i}$​
3. 确定自由度: 拟合优度检验的自由度为类别数减1；
4. 查表或使用软件得出P值：根据计算出的卡方统计量和自由度，可以查找卡方分布表，或者使用统计软件得出P值；
5. 做出结论:
    - 如果P值小于选择的显著性水平（例如，0.05），则拒绝零假设，这表明观测与预期的差异不太可能是偶然出现的。
    - 如果P值大于显著性水平，则不能拒绝零假设，这意味着观测与预期之间的差异可能仅仅是随机变异。

## Ref
- https://zhuanlan.zhihu.com/p/58556978
- Wiki - Pearson's chi-squared test, Chi-squared distribution

# RANSAC
## Idea
- Seperate the outlier from the inlier；
- 解决数据匹配的问题；
	
## Approach
- Input
	1. data points：$D$
	2. 一个可以解释观测数据的参数化模型；
	3. Hyperparameter
		- 模型需要多少点来做model fitting, $s$
		- 最大循环次数, $k$
		- fitness的阈值, $t$
		- inliner的个数阈值, $n$
- Process
	1. Sample the data points ($s$) from $D$；
	2. Model Fitting using sampled inliers;
	3. 计算剩余的点的fitness;
	4. 把fitness大于$t$的点放入inliner，当inliner过少，废弃这轮获得的内点，跳回第一步；
	5. repeat 1,2,3,4;
	6. 当inlier超过$n$时即认为算法成功;
	7. 当尝试的次数多于$k$时，则认为失败；
- Output：最佳匹配的inlier和模型，或者失败;
	
## 实验次数k的确定
- 输入
	1. Inlier Ratio, $e$
	2. Probability, $p$, 最终希望获得一个有用的模型的概率，也就是说这个模型本身不含任何outlier
	3. 模型需要多少点来做model fitting, $s$
- Approach
	1. 最终没有一个有用的模型的概率，$1 - p$；
	2. 在一次model fitting中全抽到inlier的概率，$e^s$；
	3. 一次model fitting失败的概率：即抽到的sample含有outlier的概率，$1 - e^s$
	4. $k$次model fitting失败的概率：$(1 - e^s)^k$，$1 - p = (1 - e^s)^k$
	5. 最终确定最多应该尝试的次数是：$k=\frac{\log(1-p)}{log(1-e^s)}$
- Instance
	1. ORBSLAM：
		- 在计算匹配时使用了ransac
		- 并使用卡方验证判断model fitting是否成功，查看白化过后的特征点重投影误差的平方和是否超过阈值；
	2. VINS Mono：在计算基础矩阵时使用ransac，opencv的实现；
- Ref
	- https://blog.csdn.net/robinhjwy/article/details/79174914
	- https://en.wikipedia.org/wiki/Random_sample_consensus