---
share: true
---

# Problem Formulation
- $AX = B$
- $A$: mxn, X: nx1, B: mx1
- $m < n$
	- 欠定：方程数小于未知数个数
	- 有无穷个解，添加约束，然后求解最小二乘法
- $m = n$: $X = A^{-1}B$
- $m > n$
	- 超定：测量方程数大于状态量的数量（SLAM中）
	- 无解，添加约束，然后求解最小二乘法

# m = n
	
- 高斯消元法
	- 可以理解为对$A$进行三角矩阵分解
		- $A = LU$ → 这样解线性系统比较方便;
		- 但是直接使用$LU$分解不能处理对角线元素有0的情况
	- Row Pivoting能处理上述状况
		- 代数上引入了一个permutation matrix， $P$ → $PA = LU$
		- Ref: wiki Pivot_element
- $A$是对称矩阵: 使用LDL分解 → 计算量小
- $A$ is symmetric positive definite matrix: 使用Cholesky分解 → 计算量小，不需要pivoting
- Ref：https://fncbook.github.io/v1.0/linsys/lu.html

# 超定问题
	
## Idea
- 理论上超定方程是无解的，只能寻找尽可能好的的解，最常见的即是找到误差向量范数最小的解 → $\min || B - AX ||^2$
- 如果$A$为三角矩阵，求解很容易
	
	
## 求解方法
- Normal Equation
	- Idea
		1. 最小二乘的问题可以转化为求解normal equation：$A^TAX = A^TB$ → 等价于求解$MX = N$, 其中$M$是方阵;
		2. 只要$A$是满秩的，此时的$A^TA$是一个SPD，可以利用Cholesky分解求解
	- Process
		1. 计算 $C = A^TA$
		2. 计算$d = A^Tb$
		3. 计算Cholesky分解 $C = GG^T$
	- Property
		1. 当A比较病态时，normal equation会让情况以两倍的程度变得更糟
		2. 速度最快但是数值上最不稳定
	- Ref: https://fncbook.github.io/v1.0/leastsq/normaleqns.html

- QR Decomposition
	- Process
		1. 求解$A$的QR分解 → 一般用houser holder
		2. 计算$d = Q^Tb$
		3. 求解$Rx = d$ → 这是一个square linear system，而且$R$是上三角矩阵
	- QR 算法比较稳定, 但不是能解决Rank deficiency的问题；是求解最小二乘问题的首选方法，但是比较慢
	- Ref: https://fncbook.github.io/v1.0/leastsq/qr.html
		
- SVD Decomposition
	- 能解决rank deficient的系统，计算量最大
	- Nonhomogeneous System
		- 实际上是在Least Square的框架
		- $x = VD^{-1}U^TB$
	- Homogeneous System
		- $AX = 0$ → 比如说求fundamental matrix
		- $\min \mathbf{norm}(Ax)$, st. norm(x) = 1
		- $x = av_n$ → $v_n$是V的最后一列
	- Ref
		- Singular Value Decomposition.pdf
		- The Least Squares Solution of Linear Systems.pdf
		
- Conjaguted Gradient → 优化的方法，迭代求解超大稀疏的矩阵
- Ref
	- NUMERICALLY EFFICIENT METHODS FOR SOLVING LEAST SQUARES PROBLEMS.pdf
	- https://blog.csdn.net/wangshuailpp/article/details/80209863

# 欠定问题
- Idea
	- 问题应该有无穷多个解
	- 增加限制已获得唯一的解
- Approach
	- 正则化
	- SVD Decomposition