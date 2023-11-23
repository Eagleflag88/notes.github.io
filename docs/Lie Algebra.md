---
share: true
---

# Group
- 一种集合加上一种运算的代数结构
- 集合与某种集合运算成群的条件
	1. 封闭性：集合元素经过运算后仍然属于这个集合；
	2. 结合律；
	3. 幺元；
	4. 逆；
- 实例
	1. 旋转矩阵(R)和矩阵乘法成群：	特殊正交群SO(3)；
	2. 转换矩阵(T)和矩阵乘法成群：	特殊欧式群SE(3)；
- 连续的群为李群；

# 李代数$\mathfrak{so}(3)$

## 定义：
- 每个李群都有相对应的李代数；
- 描述了李群的局部性质；
- SO(3)的李代数，是定义在$\mathbf{R}^3$上的一个向量，记作$\phi$；


## 性质

- 每个$\phi$都会对应一个反对称矩阵$\Phi$：
$$
\Phi=\phi^\wedge=
\begin{bmatrix} 
	0, & -\phi_3, & \phi_2 \\
	\phi_3, & 0, & -\phi_1 \\
	-\phi_2, & \phi_1, & 0
\end{bmatrix}
$$
- $\phi$可以理解为是瞬时角速度；
- 反对称矩阵可以表达R的导数，$R' = \Phi R$；
- 如果是用四元数表示旋转，$\mathfrak{so}(3)$对应的是纯四元数的空间；
- 指数映射：描述了李代数和旋转矩阵R的关系：
$$
R = \exp(\phi^\wedge)
$$
- 当使用轴角来表达旋转时，即获得了罗德里格斯公式；
- 指数映射$\Phi=\log(R)$；

## 对旋转求导

$\underline{微分模型}$
- 直接关于旋转矩阵的函数求导，获得下式：
$$
\frac{(R + \Delta R)p - Rp}{\Delta R}
$$
- 因为R上没有定义加法，上式无法计算；
- 李代数上定义了加法，我们可以对李代数的变化量$\delta\phi$求导，而不是对旋转矩阵的变化量$\Delta R$，获得下式：
$$
\frac{\partial(\exp(\phi^\wedge)p)}{\partial\phi}=\lim_{\delta\phi \to 0}\frac{\exp((\phi + \delta\phi)^\wedge)p - \exp(\phi^\wedge)p}{\delta\phi}
$$
- Ref: 十四讲 eq 4.40

$\underline{扰动模型}$
- 变化之后的位姿不用$\exp((\phi + \delta\phi)^\wedge)$而是用$\exp(\varphi^\wedge) \exp(\phi^\wedge)$计算
$$
\frac{\partial (Rp)}{\partial \varphi} = \lim_{\varphi \to 0} \frac{\exp(\varphi^\wedge) \exp(\phi^\wedge) p - \exp(\phi^\wedge) p}{\varphi}
$$
- 推导过程:
	- 对上式使用Tayler展开：$\exp(\delta\phi) = I + \exp(\delta\phi)$;
	- 最后利用性质 a^b = -b^a消去上式中的$\delta\phi$
- Ref: 十四讲 eq 4.41

## Implementation
- Sophus: [Ref](https://guyuehome.com/34708)