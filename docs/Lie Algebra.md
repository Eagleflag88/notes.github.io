---
share: true
---

# Group
- 一种集合加上一种运算的代数结构；
- 集合与某种集合运算成群的条件：
	1. 封闭性：集合元素相乘之后后仍然属于这个集合；
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
	0 & -\phi_3 & \phi_2 \\\
	\phi_3 & 0 & -\phi_1 \\\
	-\phi_2 & \phi_1 & 0
\end{bmatrix}
$$
- $\phi$可以理解为是瞬时角速度；
- 反对称矩阵可以表达$R$关于时间的导数，$R'(t) = R \Phi$，注意这里的结果是在主动旋转的假设下推到出来的，即旋转的是物体本身，而不是坐标系；如果旋转的是坐标系，则$R'(t) = \Phi R$。
- 如果是用四元数表示旋转，$\mathfrak{so}(3)$对应的是纯四元数的空间；
- 指数映射：描述了李代数和旋转矩阵$R$的关系：
$$
R = \exp(\phi^\wedge)
$$
- 当使用轴角来表达旋转时，$R=\exp(\theta v^\wedge)$，即获得了罗德里格斯公式；
- 指数映射$\Phi=\log(R)$；
- Ref: Time Derivative of Rotation Matrices: A Tutorial.pdf

## BCH近似

- 对标量$a,b$来说，$\exp(a)\exp(b)=\exp(a+b)$是成立的，但是对矩阵$A,B$来说，$\exp(A)\exp(B)=\exp(A+B)$是不成立的；
- 实际上$\exp(A)\exp(B)$的结果可以由BCH公式描述：
$$
\exp(A)\exp(B)=\exp(A+B+\frac{1}{2}[A,B]+\frac{1}{12}[A,[A,B]]-\frac{1}{12}[B,[A,B]]+...)
$$
- 其中$[]$是李括号，BCH公式告诉我们，当处理两个矩阵指数之积时,它们会产生一些由李括号组成的余项；
- 特别地,考虑SO(3)上的李代数的矩阵指数$\exp(\phi_1^{\wedge})\exp(\phi_2^{\wedge})$，当$\phi_1$或$\phi_2$为小量时,小量二次以上的项都可以被忽略掉。此时，BCH拥有线性近似表达：
$$
\exp(\phi_1^{\wedge})\exp(\phi_2^{\wedge}) \approx 
\begin{cases} 
\exp((J_l(\phi_2)^{-1}\phi_1 + \phi_2)^{\wedge}) & \text{if } \phi_1 \text{ is small},\\
\exp((J_r(\phi_1)^{-1}\phi_2 + \phi_1)^{\wedge}) & \text{if } \phi_2 \text{ is small}.
\end{cases}
$$

- 反过来，我们可以得到：
$$
\exp((\phi + \delta \phi)^{\wedge})=\exp((J_l\delta\phi)^{\wedge})\exp(\phi^{\wedge})=
\exp(\phi^{\wedge})\exp((J_r\delta\phi)^{\wedge})
$$


## 对旋转求导

- 假设要对函数$f(R)=Rp$关于旋转$R$进行求导；

$\underline{微分模型}$
- 直接关于旋转矩阵的函数求导，获得下式：
$$
\frac{\partial (Rp)}{\partial R}=\lim_{\Delta R \to 0}\frac{(R + \Delta R)p - Rp}{\Delta R}
$$
- 因为$R$上没有定义加法，上式无法计算；
- 李代数上定义了加法，我们可以对李代数的变化量$\delta\phi$求导，而不是对旋转矩阵的变化量$\Delta R$，获得下式：
$$
\frac{\partial(\exp(\phi^\wedge)p)}{\partial\phi}=\lim_{\delta\phi \to 0}\frac{\exp((\phi + \delta\phi)^\wedge)p - \exp(\phi^\wedge)p}{\delta\phi} \\
=-(Rp)^{\wedge}J_l
$$
- 推导过程使用了BCH的近似表达；
- Ref: 十四讲 eq 4.40

$\underline{扰动模型}$
- 如果我们对变化之后的位姿不用$\exp((\phi + \delta\phi)^\wedge)$而是用$\exp(\varphi^\wedge) \exp(\phi^\wedge)$计算（左扰动），那么结果如下：
$$
\frac{\partial (Rp)}{\partial \varphi} = \lim_{\varphi \to 0} \frac{\exp(\varphi^\wedge) \exp(\phi^\wedge) p - \exp(\phi^\wedge) p}{\varphi}=-(Rp)^{\wedge}
$$
- 推导过程:
	- 对上式使用Tayler展开：$\exp(\delta\phi) = I + \exp(\delta\phi)$;
	- 最后利用性质 $a^\wedge b = -b^\wedge a$消去上式中的$\delta\phi$
- Ref: 十四讲 eq 4.41

## Implementation
- Sophus: [Ref](https://guyuehome.com/34708)