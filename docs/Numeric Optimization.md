---
share: true
---
# Problem
- 求$F(X)$的最小值时X的值
	- $X$是一个多维变量
	- $F$一般可导
	- $X_{opt} = \arg \min F(X)$

# Approach

## 直接法
## 迭代法
- 通过计算一连串$F(X_1, X_2, X_3, ..., X_n)$的值来获得$F(X)$的最小值和此时$X$的值
- 通过调整$X$向量来使目标函数下降，能降多少降多少
- 每一个iteration需要求解的问题变为：以现有的工作点$X$为基础，求使目标函数最小的增量，$\Delta X$ → $\Delta X = \arg \min F(X + \Delta X)$

# 迭代法
## Line Search
- Idea → 对$F(X + \Delta X)$进行近似
- 一阶梯度法(最速下降法)
	- 泰勒展开后仅保留一阶项 → $F(X + \Delta X) = F(X) + J\Delta X$
	- 求上式关于$\Delta X$的导数
		1. 并令其为0
		2. 获得迭代量 → $\Delta X = -J(X)$
	- 因为过于贪心，迭代次数太多，收敛慢
- 二阶梯度法(牛顿法)
	- 保留一阶和二阶项 → $F(X + \Delta X) = F(X) + J\Delta X + (\Delta X^TH\Delta X)/2$
	- 求上式关于$\Delta X$的导数
		1. 并令其为0
		2. 获得迭代量 → $H(X)\Delta X = -J(X)$
	- $H(X)$对大规模问题来说比较难以计算
	- Gauss-Newton
		- 仅用于quadratic形式的优化问题：$\Delta X = \arg \min || F(X + \Delta X) ||^2$
		- 保留一阶泰勒展开
		- 迭代求解$J^TJ\Delta X = -JF(X)$ → 形式上对上式左侧加上一个阻尼因子就会变成LM法，但是实际上不一样，因为GN没有信任域的概念
	- Damped Method
		- 引入阻尼因子$\mu$
			- $L = F(X + \Delta X) + \mu\Delta X^T\Delta X$
			- 对过大的$\Delta X$是一种惩罚 → 相当于正则化
		- 对$L$进行泰勒一阶展开 → 求导，置零
		- 迭代求解$(J^TJ + \mu I)\Delta X = -JF(X)$
		- Ref：vio_course, lecture 3, p12

## Trust Region

- Concept
	1. 它通过在信任域内考虑目标函数的局部近似来迭代地更新当前解估计。
	2. 在Trust Region算法中，优化算法通过在信任域内考虑目标函数的局部近似来迭代地更新当前解估计。

- Process：
	1. 初始化：选择一个初始点作为算法的起始点，并设定一个初始的信任域半径。    
	2. 构建模型：在当前点的信任域内，构建一个局部近似模型来逼近目标函数。这个模型通常是目标函数在当前点的泰勒级数展开或者是一个插值多项式。    
	3. 求解子问题：在给定的信任域内，求解近似模型的最优点，得到一个候选解。这个过程通常涉及到求解一个约束优化问题，其中约束是信任域半径。    
	4. 接受或拒绝：比较候选解和当前解的目标函数值，如果候选解比当前解更好，则接受候选解作为新的当前解，并扩大信任域半径以在下一轮迭代中探索更大的区域。否则，保持当前解不变，并缩小信任域半径以在下一轮迭代中更加集中地搜索。    
	5. 终止条件：重复上述步骤，直到满足某个终止条件，例如达到最大迭代次数、目标函数的改进小于某个阈值或者信任域半径小于某个阈值等。
- Instance
	1. Dog Leg，详见[[Least Square Problem#Nonlinear Case|Least Square Problem > Nonlinear Case]]的Dog Leg；
