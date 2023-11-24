---
share: true
---
# Multivariate Gaussian Distribution
- p(X) = exp[trans(X - MIU)*inv(COV(X, X))*(X - MIU)*(-0.5)]
- X：多元变量
- 在SLAM中很多时候应该处理成0均值的变量
- MIU：均值向量
- COV(X, X)：协方差矩阵
- 信息矩阵，LAMBDA = inv(COV) → 信息矩阵的元素如果为零表明其代表的变量不相关

# 舒尔补
## Idea
- 考虑任意方阵M = [A B; C D]
- 通过定义舒尔补可以快速求得M的逆阵
- 条件是A和D矩阵可逆

## Derivation
- 考虑线性系统M*[x; y] = [c; d]
- 利用消元法求解线性系统，得到x和y的表达式
	- x = inv(A - B*inv(D)*C)*c - inv(A - B*inv(D)*C)*B*inv(D)*d
	- y = -inv(D)*C*inv(A - B*inv(D)*C)*c + (inv(D) + 
      inv(D)*C*inv(A - B*inv(D)*C)*B*inv(D))*d
- 观察上式可获得inv(M)，因为[x; y] = inv(M)*[c; d]
- Ref: The Schur Complement and Symmetric Positive Semidenite (and Denite) Matrices.pdf

## Property
- 实际上就是Variable elimination in a probabilisitic view
- 消元之后，被边缘化的结点包含的信息被转移到剩余的结点中，总体的信息量保持不变
- 但是消元之后，当有新的跟老的状态相关的测量值时，已经被丢弃的老的状态无法更新
- 经过marginalization之后，保留的状态的信息矩阵会出现变化

# Application
## 滑窗管理
- Problem
	- 滑窗中a和b分别是需要被marg和留下的状态
	- p(a, b)的协方差矩阵
		- M = [A, B; C, D]
		- A = COV(a, a)
		- D = COV(b, b)
	- 目标
		- 条件概率：p(b|a) → 即边缘化后的概率分布
		- 边际概率：p(a) → 副产品
- Usage
	- 从协方差矩阵M中获得概率分布
		- p(a)的协方差矩阵是A
		- p(b|a)的协方差矩阵是A的舒尔补 → D - C*inv(A)*B
		- Ref：vio_course 第四节 eq32
	- 从信息矩阵inv(M)中获得概率分布
		- inv(M) = [LAMBDA_aa LAMBA_ab; 
                LAMBDA_ba LAMBA_bb] → 可以通过舒尔补快速获得
            - p(a)的协方差矩阵是A → LAMBDA_aa - LAMBDA_ab*inv(LAMBDA_bb)*LAMBDA_ba
            - p(b|a)的信息矩阵是LAMBDA_bb → 我们想要的
            - Ref：vio_course 第四节 eq 37，38
- 同时会让BA的normal matrix变得更稠密

## BA
- Problem：求解H*delta_X = G
	- H: Normal Matrix：H = [B E; trans(E) C]
		- B为位姿矩阵
		- C为路标
		- B比C的维数小很多
	- delta_X = [delta_c; delta_p]
	- G = [v; w]
- Approach
	- Idea：求解reduced linear system, 减少计算量
		- pose的数量比feature数量少很多
		- Reduced linear system就是关于pose的
	- Process
		- 求C的逆阵inv(C)
		- 利用舒尔补获得关于位姿的增量方程
			1. [B - E*inv(C)*trans(E)]*delta_c = v - E*inv(C)*w，reduce camera system。S = [B - E*inv(C)*trans(E)] → 一个半正定矩阵
			2. 问题规模与B一样，规模不大
			3. 根据S的稀疏性，ceres求解器会选择dense/sparse solver
				- Sparse cholesky decomposition
				- Preconditioned conjugate gradient
			4. Ref：十四讲 eq 10.59
		- 得关于路标的增量方程 → $\Delta p = inv(C)*(w - trans(E)*delta_c)$
## Null-space operation
- 对需要被边缘化的变量的residual jacobian利用其null space vector来消除，MSCKF
- Ref: Null-Space-based Marginalization Analysis and Algorithm