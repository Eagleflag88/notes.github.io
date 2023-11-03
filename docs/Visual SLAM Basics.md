---
share: true
---
# Camera Model

## Pin-hole model

### Concept
- 现实世界物体：相机坐标系下的表示$P_c=[x, y, z]$
- 焦距 f：光心到物理成像平面的距离
- 像素点：$P_{uv}=[u, v]$，固定于物理成像平面的像素平面上。像素是成像之后的点经过采样和量化之后生成的，物理成像平面相差一个缩放和偏移。其由现实世界物体经过相机内参K获得，定义为：
  $$ 
 K=\begin{bmatrix} 
   \alpha_x & x_0      & 0  \\
   0        & \alpha_y & y_0  \\
   0        & 0     & 1  \\
 \end{bmatrix}
 $$
其中，$\alpha_x$和$\alpha_y$是x和y轴的scaling factor，$x_0$和$y_0$是光轴和归一化平面的交点在归一化平面的坐标点
只能和归一化的空间点相乘得到像素坐标

### Usage

$\underline{世界坐标到相机坐标}$
1. 世界坐标系的物体$P_w=[X, Y, Z, 1]$
2. 利用相机位姿求得相机坐标系下的坐标，$P_c=TP_w=[X_c, Y_c, Z_c, 1]$，其中$T$是4x4相机位姿矩阵。
3. $P_c$和$P_w$都是齐次坐标。

$\underline{相机坐标到像素坐标}$
1. 空间点归一化：把$P_c$前三位取出，然后归一化，获得$norm(P_c)$，这个点在归一化平面上。
2. 利用$P_{uv} = K*norm(P_c)$获得像素平面的像素坐标$P_{uv}$，可以看做是归一化空间点在像素平面的成像和采样。

$\underline{Projection \ Matrix}$
1. 建立从世界坐标到像素坐标的关系，把上面的两个转换结合起来，$P_{uv}=P*P_w$。
2. 利用$P_{uv} = K*[R|T]*P_w$，我们可以得到$P = K*[R | T]$，是一个3x4的矩阵。
3. Ref：Multiple View Geometry in Computer Vision, eq 6.8。

## Fisheye Model

## 畸变

### 畸变模型
- 径向畸变：由透镜边缘的形状引起，可以分为桶形畸变和枕形畸变。可以利用多项式描述
$$ 
x = x(1 + k_1r^2 + k_2r^4 + k_3r^6)
$$
其中r是归一化平面上离中心点的距离。
- 径向畸变：可以利用多项式描述
$$ 
x = x + 2p_1xy + p_2(r^2 + x^2)
$$
- Ref 十四讲 eq 5.11，5.12

### 去畸变
1. 把空间点投到归一化平面，$norm(P_c)$。
2. 把归一化平面上的空间点坐标进行径向和切向修正，$norm(P_c)_{corr}$。
3. 把归一化平面上的像素点通过内参矩阵投影到像素平面，$P_{uv,corr}$

### 双目

$$z=\frac{fb}{d}$$
其中，z：深度，f：焦距，b：baseline和视差反比关系。d：视差$u_L - u_R$。

## RGB-D
能够在线的获得每个像素的深度值。成像方式包括：
- 主动发射红外光
- 结构光：通过分析返回的结构光图案获得距离
- ToF：分析返回的时间

容易受环境影响，不适合室外SLAM

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

# Transform

## Vector
 
- 内积: a*b：b向量在a向量方向上的投影，计算公式为：
$$
a*b = |a||b|cos(a, b)=\sum{a_ib_i}
$$
其结果是一个标量。
- 外积: axb：结果是方向垂直于这两个向量的向量，a, b和axb构成右手系。大小由两个向量的夹角决定，长度为|a|*|b|*sin(a, b)。可以用来表示旋转。
- 反对称矩阵: a^，定义如下：
$$
a=\begin{bmatrix} 
   0 & a_1 & -a_2  \\
   -a_1 & 0 & a_3  \\
   -a_2 & -a_3 & 0  \\
 \end{bmatrix}
$$
特征是对角线元素为0，上下三角元素取反。可以用来表示向量的外积操作, a^*b = axb。

## Rotation Matrix, R

$\underline{Property}$
- 3x3的矩阵。
- 满足$RR^T = I$ (单位矩阵) 。
- det(R) = 1。
- 属于正交矩阵群，转置即逆阵。
- 过参数化的定义，而且会在优化问题中引入限制，即必须是正交矩阵，行列式为1。

$\underline{Usage}$
- 左乘对向量a进行旋转，$R_{rot}=Ra$。
- 当在优化问题中使用时，会因为本身是带约束的变量，会把无约束优化问题变为有约束的优化问题

## Transform Matrix, T
	T = [R t; 0 1]
	与齐次坐标配合使用可以表示连续的坐标变换
		T1*T2*a
		a是齐次坐标
