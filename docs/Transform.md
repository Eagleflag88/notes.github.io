---
share: true
---

# Landmark

$\underline{欧式距离}$
$$P=[x,y,z]$$

$\underline{逆深度}$
- 定义：相机坐标系下的空间点深度的逆：
$$\lambda = \frac{1}{z}$$
同时包含第一次观测到这个路标点时的camera pose。

- 优势
1. 能表示无限远处的点
2. 在初始化时的相对观测量的线性化程度高

- 劣势
1. 参数需要带上第一次观测的相机pose
2. 计算量大，倒数运算

- Ref：Inverse Depth Parametrization for Monocular SLAM.pdf

# Vector
 
- 内积: $a*b$：b向量在a向量方向上的投影，计算公式为：
$$
a*b = |a||b|cos(a, b)=\sum{a_ib_i}
$$
其结果是一个标量。
- 外积: axb：结果是方向垂直于这两个向量的向量，a, b和axb构成右手系。大小由两个向量的夹角决定，长度为$|a||b|sin(a, b)$。可以用来表示旋转。
- 反对称矩阵: a^，定义如下：
$$
a=\begin{bmatrix} 
   0 & a_1 & -a_2  \\
   -a_1 & 0 & a_3  \\
   -a_2 & -a_3 & 0  \\
 \end{bmatrix}
$$
特征是对角线元素为0，上下三角元素取反。可以用来表示向量的外积操作, $a^*b =$。

# Rotation Matrix

$\underline{Property}$
- 3x3的矩阵。
- 满足$RR^T = I$ (单位矩阵) 。
- det(R) = 1。
- 属于正交矩阵群，转置即逆阵。
- 过参数化的定义，而且会在优化问题中引入限制，即必须是正交矩阵，行列式为1。

$\underline{Usage}$
- 左乘对向量a进行旋转，$R_{rot}=Ra$。
- 当在优化问题中使用时，会因为本身是带约束的变量，会把无约束优化问题变为有约束的优化问题

# Transform Matrix
- Transform可以用4x4矩阵表示：
$$
\left[\begin{array}{ll}
\boldsymbol{R} & \boldsymbol{t} \\
\mathbf{0}^{T} & 1
\end{array}\right]
$$

- 与齐次坐标配合使用可以表示连续的坐标变换，比如$a^\prime=T_1T_2a$；
- 上式描述利用$T_1$和$T_2$对空间点a进行变换，成为$a^\prime$，其中a是齐次坐标：
$$
a=
\begin{bmatrix} 
	x \\
	y \\
	z \\
	1
\end{bmatrix}
$$

# Rotation Vector

- 也被称为轴角Axis-Angle；
- Idea：任何旋转都可以通过一个旋转轴和旋转角来刻画；
- Property：方向与旋转轴一致，大小等于旋转角，通过下式定义：
$$
v=\theta n
$$
其中，
1. $\theta$表示旋转角度，标量，DoF=1；
2. $n$是与旋转轴一致的单位向量，DoF = 2。$n$是旋转矩阵R在特征值为1时的特征向量，在旋转轴上的向量经过旋转之后不会发生变化，满足$Rn = n$
3. 实际上就是旋转矩阵的李代数，可以用罗德里格斯公式转换为旋转矩阵；

# 欧拉角
$\underline{Idea}$
- 使用分离的三个角度，把一个旋转分解为围绕三个轴的转动；

$\underline{Property}$
- Pitch, Roll, Yaw
- 会有万向锁引起的奇异性问题，当pitch等于$\frac{\pi}{2}$时，经过roll，pitch和yaw的旋转后，最终的旋转矩阵只和(roll - yaw)有关，即丢失了一个自由度；
- 欧拉角是旋转的最小的参数化表示，但是有奇异性；
- 很难/几乎不可能用来表示旋转组合；
- Ref：https://www.euclideanspace.com/maths/geometry/rotations/euler/index.htm

# Quaternion
## Idea
- 通过复数来表示旋转

## Property
- 由一个实部和三个虚部组成，每个虚部代表一个轴，定义
$$\mathbf{q} = q_0 + q_1i + q_2j + q_3k$$
- 所有的三维旋转都可以由一个单位四元数表示；
- $\mathbf{q}$和$-\mathbf{q}$表示同一个旋转，实质上是多转了$2\pi$；
- Identity Quaternion：实部为1，虚部为0。表示没有旋转；
- 单位四元数和轴角的关系，如下式：
$$
\mathbf{q} = 
\begin{bmatrix} 
	cos(\theta/2) \\
	sin(\theta/2)n_x \\
	sin(\theta/2)n_y \\
	sin(\theta/2)n_z 
\end{bmatrix}
$$
- 其中，角轴v的旋转向量是：
$$
n=
\begin{bmatrix} 
	n_x \\
	n_y \\
	n_z 
\end{bmatrix}
$$
- 由此看出四元数只表示了一半的旋转；
- 四元数是最小的过参数化定义；

## Operation

- 加，减：Trivial；
- 两个四元数相乘：不满足交换律；
- 共轭：$q^*$，虚部取反；
- 模：所有系数的平方根；
- 逆： 定义为$q^{-1}=\frac{q^*}{||q||^2}$，表示q的反向旋转；
	1. 其满足：$q^{-1}q = qq^{-1} = 1$；
	2. 对单位四元数来说，$q^*$和$q^{-1}$一样；
- 指数映射：
$$
e^q=e^{q_0}()
$$
- 对数映射：
$$
\log(q)=
\begin{bmatrix} 
	0 \\
	usin(\theta)
\end{bmatrix}
$$

## Rotation Action

- 被转换的点$a = [x, y, z]$，a转化为一个虚四元数：
$$
a=
\begin{bmatrix} 
	0 \\
	x \\
	y \\
	z
\end{bmatrix}
$$
- 旋转：利用一个单位四元数定义：
$$
q=
\begin{bmatrix} 
	cos(theta/2) \\
	sin(theta/2)u
\end{bmatrix}
$$
它表示一个围绕向量u旋转theta/2角的旋转;
- 旋转后的点：
$$
a_{rot} = qaq^{-1}
$$

## Rotation Combination
- 四元数的旋转组合涉及将两个旋转合并为一个单独的旋转。
- 如果有两个四元数$q_1$和$q_2$，分别代表两个旋转，那么它们的组合旋转 $q$ 可以通过四元数乘法来实现：
$$
q=q_2q_1
$$

- 这里$q_2$是第二个执行的旋转，$q_1$是第一个执行的旋转。
- 重要的是要注意四元数乘法不是交换的，即 $q_2q_1$与$q_1q_2$ 的结果是不同的。

## Convention
- 分为Hamilton和JPL系，他们相同的点是：
$$
i^2 = j^2 = k^2 = -1
$$
- 不同点是：
	- Hamilton：$ijk = -1$，右手系，prefered；
	- JPL：$ijk = 1$

