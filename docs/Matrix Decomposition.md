---
share: true
---

# Special Matrix
## ONC Matrix
- Matrix that has orthonormal columns，列向量之间是正交的；
- Property：
	1. $M$和他的转置之积为单位矩阵，$M^TM=I$
	2. 由上条推出：$M^{-1}=M^T$
- 几何上来说，正交矩阵进行了旋转变换，也就是坐标变换
- 当$M$是方阵时被称为Orthogonal Matrix
- Ref: https://fncbook.github.io/v1.0/leastsq/qr.html
	
## Singular Matrix，$M$
- 仅仅针对方阵而言
- $\det(M) = 0$
- 不可逆
- 至少有一个线性相关的行和列
- 从几何角度来看，奇异矩阵对应于将空间压缩至更低维度的转换。例如，一个将三维空间中的所有点压缩到一个平面上的转换对应于一个奇异矩阵。这种转换是不可逆的，因为一旦空间被压缩，就无法恢复原始的三维结构；

## Diagonal Matrix
- 几何上来说，对角矩阵对每个坐标轴进行缩放
	
## 三角矩阵
- 主对角线以上/下(不包括主对角线)的元素均为常数0 → 几何上来说是斜向切边；
- 只要对角线上有为0的元素，则矩阵本身为奇异矩阵 → Triangular Singularity；

## Symmetric Matrix
- $A^T=A$
- 能够利用$LDL^T$分解达成$LU$的效果，而且只需要一般计算量;
- 但是需要pivoting;

## Symmetric positive-definite Matrix
- 对所有非零向量$X$，都有$X^TAX>0$;
- 适合Cholesky Decomposition
	1. 不需要pivoting就能稳定;
	2. 计算量是$LU$分解的一半;

## Psudoinverse
- $pinv(A) = [A^TA]^{-1}A^T$
- 当$A$是矩形矩阵时，形式上用来表示为$A$的逆阵;
- 线性系统的解可以广义的利用$X = pinv(A)B$表示;

# LU Decomposition
- Limited for square matrices, $A = LU$
	- $U$: Upper Triangulation
	- $L$: Low Triangulation
- Found using an iterative numerical process → 基本上就是Guassian Elimination + row pivoting
- Used to simplify the solving of systems of linear equations
- Variant with numeric stability
	- $A = PLU$, $P$是Permutation Matrix
- Application
	- Solving the linear system → 计算量最大，基本上不考虑;
	- Ref: https://www.geeksforgeeks.org/l-u-decomposition-system-linear-equations/

# Cholesky Decomposition
- Defined for symmetric and positive definite matrix;
- 每个positive definite matrix都能做Cholesky Decomposition，得到：
	- $A = UU^T$ or $A = LL^T$
	- $U$或者$L$可以理解为$A$的均方根;
- Used for solving linear least squares problem → $A^{-1} = (L^{-1})^TL^{-1}$;
- 能够利用系统的稀疏性，是normal equation法的一个步骤
- Ref:
	- 分解过程参考：The Cholesky factorization.pdf
	- [Wiki](https://en.wikipedia.org/wiki/Cholesky_decomposition)

# $LDL^T$ Decomposition
- $A = LDL^T$
	- $L$: nxn，下三角单位矩阵;
	- $D$: 对角矩阵
- 跟Cholesky分解一样使用于对称正定矩阵；
- 几何上来说就是先进行旋转然后再进行切变拉伸；
- 计算的时候无须像Cholesky一样计算平方根，因此在某些情况下可能更为稳定；
- 计算复杂度略高于Cholesky分解，因为它涉及对角矩阵的计算；

# QR decomposition
- Structure
	- $A = QR$
		- $A$: mxn
		- $Q$: mxm，正交矩阵
		- $R$: mxn upper triangle matrix
	- 几何上来说就是先进行旋转然后再进行切变拉伸
- Decomposition
	- 利用Givens 旋转、Householder 变换，以及 Gram-Schmidt正交化获得；
		1. Modified Gram-Schmidt正交化:	基本上只有理论推导使用,数值上不稳定;
		2. Householder 变换: 对某一向量进行镜面变换, 镜面由其法向量确定；
		3. Givens 旋转: 稀疏矩阵问题,尤其是带状矩阵问题有特别的优势；
	- Ref: https://mathmu.github.io/MTCAS/doc/QRLS.html
- Application:
	1. Solving of systems of over-defined linear equations → 数值上最稳定，但是最贵；
	2. Ref: http://qucs.sourceforge.net/tech/node99.html

# Eigenvalue Decomposition
	
## Eigenvalue
- $Av = \lambda v$
- $v$ is the eigen vector of $\lambda$。也就是每一个$\lambda$对应一个$v$
- When a linear transformation $A$ acts on one of its eigenvectors $v$, it shrinks the vector or stretches it and reverses its direction if $\lambda$ is negative, but never changes the direction otherwise;

## Structure
- $A = QDQ^{-1}$
- $A$必须为方阵nxn
- $Q$: nxn，是$A$的所有特征向量组成的矩阵;
- $D = diag(\lambda)$，是特征值组成的矩阵；
- Ref: http://fourier.eng.hmc.edu/e176/lectures/NM/node8.html

## Process
- 寻找特征值
	1. 特征方程：首先，设定特征值方程 $Av = \lambda v$。将其重写为 $(A - \lambda I)v = 0$；
	2. 求解行列式：为了找到特征值，我们需要解这个方程的行列式，即计算$\det (A - \lambda I) = 0$，这是一个关于$\lambda$的多项式方程；
	3. 求解多项式：求解上述方程，得到的根就是矩阵$A$的特征值。对于大多数非平凡矩阵，这需要数值方法来求解；
- 寻找特征向量
	1. 代入特征值：对于每一个找到的特征值 $\lambda_i$​，将其代入方程$(A - \lambda_i I)v = 0$;
	2. 求解线性方程组：求解上述线性方程组以找到对应的特征向量 $v_i$​。通常，这意味着找到方程组的非平凡解;
- 构造分解矩阵
	1. 构建矩阵$Q$ 和$D$：将所有特征向量放置在矩阵$Q$的列中，并将对应的特征值放置在对角矩阵$D$的对角线上;
	2. 特征值分解：最终，矩阵$A$可以表示为$A = QDQ^{-1}$;

# Singular Value Decomposition
## Structure
- $A = UDV^T$
	- $A$, MxN
	- $U$，正交矩阵，MxM
	- $D$，对角矩阵，MxN
		1. Diagonal elements in $D$ are singular value
		2. 对角线中的非0元素个数是$A$的Rank
		3. 实际上是$A^TA$矩阵的eigenvalue
		4. 可能是矩形
	- $V$，正交矩阵，NxN
- 几何上来说就是先进行旋转，然后缩放最后再旋转
- 任意矩阵都能进行SVD变换
	
## Decomposition
1. 求$A^TA$的eigenvector;
2. 利用求得的eigenvector构建$U$和$V$;
- Ref: Singular Value Decomposition.pdf

## Singular Vector/Value
- The singular vectors of a matrix describe the directions of its maximum action
- Singular values describe the magnitude of that action
- 如果$A$把单位圆变成椭圆，那singular value就是椭圆两个方向的轴距
- Each set of singular vectors will form an orthonormal basis for some linear subspace
- 如果$A$是square， eigen value decomposition 和 svd是等效的
- Ref: https://mathformachines.com/posts/eigenvalues-and-singular-values/

## Null space
- 如果$A$的秩为k, null space是由在$A$之中除了k之外的列向量组成的空间 → 矩阵的秩是矩阵中线性独立的向量的数量；
- Nullity：Null space的维度
- 在null space之中，$Ax = 0$ → 即$Ax = 0$解就是$A$的null space

## Application
- Low Dimensional Approximation of Matrix:
	1. By setting some of the singular value to 0
	2. 矩阵维度会降低
- PCA: By setting the smallest singular value to 0
- 求逆: $A^{-1} = VD^{-1}U^T$
- Solving the linear system