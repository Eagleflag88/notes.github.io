---
share: true
---

# 2D-2D
## Fundamental Matrix
$\underline{Property}$
- encode relative orientation between two uncalibrated images, 3x3 Matrix
- describe coplanarity constraint of the feature points
- $x^TFx'=0$：
	1. homogenous coordinate $x$ and $x'$ in pixel plane；
	2. $x = [u, v, 1]$
- Rank(F) = 2：因为Rank(E) = 2
- DoF(F) = 7：
	1. Scale的模糊性减去一个
	2. $\det(F) = 0$减去一个

$\underline{8 \ Point \ Algorithm}$
- Turn matrix to vector of unknowns
- 只利用了线性关系：理论上需要8个点，去除scale模糊性；
- 求解齐次线性系统$AF = 0$
	1. 通过在$||F|| = 1$的框架下最小化$||AF||^2$；
	2. 使用SVD
		- $A = UDV^T$
		- $F$是$V$的最后一列
- 修正$F$
	1. 测量数据会有噪声，导致Rank(F) != 2
	2. 算是通过这种方式变相利用了奇异限制，因为$D$的非零对角线元素就是$F$的奇异值；
	3. Calculate SVD of $F$，$F = UDV^T$;
	4. Enforcing Rank of $F$ to 2, 置零最小的奇异值;


$\underline{Numeric \ Consideration}$
- 当pixel坐标采用HC时，比如$p=[x, y, 1]$， 当x和y过大时，会造成数值上的不稳定，应当首先进行归一化进行
- Conditioning:
	1. Transform, so the center mass is (0, 0)
	2. Normalization of the pixel point to (-1, 1)
- Degeneration:
	1. Point on a Plane：基础假设不成立，需要用单应矩阵处理；
	2. Pure Rotation：$t = 0$，无法求解出$E$


$\underline{Other \ approaches}$
- 7-point algorithm
- Minimize reprojection error: Golden Standard, 其初值可以从8点法或者7点法获得


## Essential Matrix
$\underline{Property}$
- Fundamental Matrix for calibrated camera
- $x^TEx' = 0$:
	1. 在不同的$x$尺度下都是成立的，尺度不变性；
	2. $x$和$x'$都是归一化的空间点坐标；
- $E = t^\wedge R$
	1. 奇异值一定是$[d, d, 0]$，因为$t^\wedge$的结构导致奇异值如此分布；
	2. Rank(E) = 2，因为尺度模糊性，Rank($t^\wedge$) = 2
- $F=K^{-1}EK^{-1}$
- 5 DoF: 表达旋转和平移加起来有6个DoF，由于尺度不变形，减一个；

$\underline{8 \ Point \ Algorithm}$
- 只利用了线性关系，理论上需要8个点；
- $E$的计算跟$F$一致
- 修正$E$：
	1. 利用$E$的非线性；
	2. 其中令奇异值矩阵为$[d, d, 0]$；

$\underline{运动恢复}$
- 对$E$进行SVD分解
- 可获得4个组合的$t$和$R$，得到的$t$只能up to a scale；
- 根据空间点在相机深度的positivity来筛选$t$和$R$的组合

$\underline{5 \ Point \ Algorithm}$
- Golden Standard for Motion Estimation
- Used together with RANSAC：每次算法需求的点数越少，所需的时间变少；

$\underline{Ref}$
- 十四讲 eq 7.10 - 7.14
- [Wiki](https://en.wikipedia.org/wiki/Essential_matrix)


## Homography Matrix
$\underline{Property}$
- 描述两个平面之间的关系
- 适用于特征点在一个平面的情况
- 对同一个平面上的两个特征点有$p_1 = Hp_2$
	- $p_1$和$p_2$为像素点；
	- $H$为3x3的单应矩阵；
	- DoF为8，因为尺度不变性；

$\underline{DLT}$
- 利用上式构造线性方程组$AH = 0$
	1. 十四讲 eq 7.20
	2. 每对匹配点可以构造出两个方程
	3. 理论上，4组点对即可求出单应矩阵
- 利用SVD求解出单应矩阵

$\underline{运动恢复}$
- $H = R + tn^T$，n为平面法向量；
- 利用SVD分解出$R$和$t$；
- Ref：Deeper understanding of the homography decomposition for vision-based control.pdf

# 3D-2D
## Problem
- 已知n对3D-2D匹配点
	- 3D点为世界坐标，$P_w$
	- 当内参已知，2D点为归一化平面的坐标，$P_c = [u, v, 1]$
	- 当内参未知，2D点为像素平面的坐标，$P_{uv} = [u, v]$
	- 以下推导为假设内参$K$已知
- 对每一个点对，$P_w$, $P_c$, $s$: $sP_c = KPP_w$
- $P$是形式为$[R|t]$的3x4的投影矩阵
	1. 注意不是变换矩阵
	2. 尽管P的自由度只有6，但是我们先按有12个未知数计算
- 每一对匹配点消除2个DoF，最少需要3个匹配点对；
	
## Approach
- $n = 3$：Closed-Form Solution；
- $n > 3$：Optimization based Approach
- Using RANSAC to remove outliers

## Variante

$\underline{DLT}$
- Process
	- 求解投影矩阵：
		1. 找到6个以上点对，因为仅仅利用到了代数性质，所以需要6个点对；
		2. 利用投影方程列出等式，并转化为$AX = 0$的形式；
		3. 利用SVD分解$A$求解获得$X$，$X$为$V$的最后一列；
	- 恢复R和t
		1. $X = s[R, t]$，$X$是有尺度的；
		2. 对$X$进行SVD分解：$X = UDV^T$
		3. 通过空间点应该在摄像机前获得$R$和$s$的符号
- Ref: https://zhuanlan.zhihu.com/p/58648937

$\underline{P3P}$
- 世界坐标系下的三点和图像坐标系下的三点匹配
- 通过三角余弦定理得出二元二次方程;
- 问题转化为3D-3D的问题，有4个解;
- 只能利用3个点的信息

$\underline{EPnP}$
- Idea
	1. 需要4对不共面的（对于共面的情况只需要3对）3D-2D匹配点
	2. 任何一个3D点都可以表示为四个控制点的线性组合，线性组合的权重之和为1
- Ref: https://zhuanlan.zhihu.com/p/59070440

$\underline{BA}$
- 重投影误差
- Ref: 十四讲，7.7.3

# 3D-3D
## Problem
- 已知n对3D-3D匹配点：3D点为齐次世界坐标，$P_w$
- 对每一个点对，$P_1$,$P_2$
	1. $P_2 = TP_1$
	2. $T = [R, t; 0, 1]$

## Approach
- 代数方法
	1. 计算两个点云质心
	2. 构造$W$矩阵
	3. 对$W$进行SVD分解，求$R$
	4. 基于$R$求$t$
	5. Ref: https://zhuanlan.zhihu.com/p/63964953
- 基于优化的方法
	1. 使用李代数表达位姿
	2. 误差函数为点对的欧氏距离

# Rolling Shutter Camera
## Property
- 每个特征点的捕捉时间/位姿是不一样的
- 每行像素捕捉的时间是不一样的
- causes unmodeled noises and outliers in feature points matching

## Approach
- 创建一个关于时间的feature的观测模型
	1. 跟运动速度相关：IMU可以contribute
	2. 每个feature的pose可以通过插值速度模型得到
- 求解优化问题：初始值来自于传统方法
- 结合RANSAC
- Ref：Rolling Shutter Bundle Adjustment.pdf