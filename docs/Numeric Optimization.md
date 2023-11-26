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
- Idea：对被优化的函数进行使用二阶泰勒展开去近似
- Levenberg-Maquadt