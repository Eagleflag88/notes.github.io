---
share: true
---
# Ref

[交叉熵损失](https://blog.csdn.net/tsyccnh/article/details/79163834)

# Cross-Entropy Loss

交叉熵损失（Cross-Entropy Loss）是评估分类模型性能的一种常用方法，特别是在二分类和多分类问题中。它衡量的是模型预测的概率分布与实际标签的概率分布之间的差异。

$\underline{Preliminaries}$

- **概率分布$p\left(x\right)$的熵**：

$$
H(X)=-\sum_{i=1}^{n} p\left(x_{i}\right) \log \left(p\left(x_{i}\right)\right)
$$
其中，$p\left(x_{i}\right)$是某个事件发生的概率，而$\log \left(p\left(x_{i}\right)\right)$则是这个事件发生所包含的信息量；所以概率分布的熵可以理解为信息量的期望；

- **两个概率分布$p(x)$和$q(x)$的DL散度**：

$$
D_{K L}(p \| q)=\sum_{i=1}^{n} p\left(x_{i}\right) \log \left(\frac{p\left(x_{i}\right)}{q\left(x_{i}\right)}\right)
$$
DL散度描述了两个概率分布$p(x)$和$q(x)$之间的距离。简化了之后得到：
$$
D_{KL}(p||q) = \sum_{i=1}^{n} p(x_i)\log(p(x_i)) - \sum_{i=1}^{n} p(x_i)\log(q(x_i))

= -H(p(x)) + \left[ -\sum_{i=1}^{n} p(x_i)\log(q(x_i)) \right]

$$
上式中第一部分是$p(x)$的熵，第二部分是$p(x)$和$q(x)$之间的交叉熵。那么，预测概率$q$和真值标签$p$之间的交叉熵损失为：

$$
L(p,q)= -\sum_{i=1}^{n} p(x_i)\log(q(x_i))
$$

$\underline{Example}$

对一个3个类别的分类问题的一个样本来说
- Input：
	1. 样本的真值概率：$p_{gt}=(0, 0, 1)$；
	2. 样本的预测概率：$p_{pred}=(0.1, 0.2, 0.7)$；
- Process：按照上式计算交叉熵Loss；
- Output：交叉熵Loss；

# Softmax

交叉熵损失（Cross-Entropy Loss）的计算之前通常会需要softmax操作，尤其是在处理多分类问题时。这样做的原因是交叉熵损失要求输入是概率分布，而模型的原始输出，通常称为logits，通常并不满足这一点。softmax函数将logits转换为概率分布，这样就可以与真实的标签概率分布进行比较了。上面的$p_{pred}$即是softmax的操作的结果；

softmax函数的定义是：
$$
\text{softmax}(z_i) = \frac{e^{z_i}}{\sum_{j} e^{z_j}}
$$

这里，$z$ 是模型的logits向量，$z_i$​ 是该向量中的第i个元素。

数值计算：
- 问题1：
	1. 当$z_i$太大且大于0，直接计算$z_i$的指数会让计算Overflow；
	2. 当$z_i$是负数且很大，会让$z_i$的指数趋近于0。如果在分母发生会导致除零。如果在分子发生会导致取log之后输出负无穷；
- Approach：把$z_i$替换为$z_i-\max(z_i)$；

# Focal Loss
Focal Loss是一种专门为解决类别不平衡问题而设计的损失函数。它是交叉熵损失函数的一个变种，通过增加一个因子来减少易分类样本的相对损失，从而使模型更加关注难分类或者错误分类的样本。

Focal Loss的数学定义为：

$$
FL(p_t) = -\alpha_t (1 - p_t)^\gamma \log(p_t)
$$

其中：

- $p_t$​ 是模型对于实际标签 t 预测为正确类别的概率，定义为：
$$
p_t = 
\begin{cases} 
p, & \text{when } y = 1 \\
1-p, & \text{when } y = 0 
\end{cases}
$$
- 其中，$p$指的是$y=1$的概率；
- $\alpha_t$​ 是正样本的权重，用于调节正负样本的重要性，可以是一个常数或者是类别的权重。
- $\gamma$是调节因子，称为聚焦参数（focusing parameter），它的作用是减少容易分类样本的损失的影响，并使模型更加关注难以分类的样本。

当 $\gamma=0$ 时，Focal Loss退化为标准的交叉熵损失；当 $\gamma>0$ 时，调节因子$(1-p_t)^\gamma$ 会降低易于分类样本的损失贡献，增加了对困难或错误分类样本的惩罚。具体来说：
- 当$p_t$比较小：这个样本比较难，其Loss会被配以比较大权重；
- 当$p_t$比较大：这个样本比较简单，其Loss会被配以比较小的权重；


Focal Loss特别适合于处理那些在类别分布极度不平衡的情况下的目标检测问题，比如在一幅图像中背景类的样本远多于前景类的样本。它使得模型在训练过程中更加关注那些难以正确分类的样本，从而提高整体的模型性能。

# Quality Focal Loss

- 普通的Focal Loss有如下问题：
	1.  

# Smooth L1 Loss

平滑L1损失（Smooth L1 Loss）是一种广泛用于回归问题中的损失函数，特别是在目标检测的边界框回归任务中。这种损失函数结合了L1损失（绝对值损失）和L2损失（平方损失）的优点，目的是减小对异常点的敏感度，并在误差较小时提供更平滑的梯度。

平滑L1损失在数学上定义为：

$$
\begin{cases} 0.5 x^2 & \text{if } |x| < 1, \\ |x| - 0.5 & \text{otherwise.} \end{cases}
$$

- 当预测误差较小时，损失函数采用平方形式，使得梯度相对较小，从而对模型参数的更新更加平滑； 
- 当预测误差较大时，损失函数切换为线性形式，减少了对离群点的敏感性，因为不再对大误差进行平方放大，避免了L2损失在面对大误差时梯度过大的问题；

# IoU Loss

IoU损失（Intersection over Union Loss），又称为Jaccard损失，是一种在目标检测和图像分割任务中常用的损失函数，用于评估预测边界框或分割区域与真实值之间的重叠程度。IoU本身是一个度量标准，用于计算预测区域和实际区域的交集与它们的并集之比。

IoU损失是1减去IoU的值，用于在优化过程中最大化IoU度量：

$$
IoU \ Loss=1−IoU
$$


IoU损失鼓励模型增加预测区域与真实区域的重叠，从而提高预测的精确度。

## 可导性

然而，IoU损失在预测区域和真实区域没有交集的情况下不可导，因为在这种情况下，IoU的值为0，对预测边界框的参数的微小变化不会影响IoU的值，即梯度为0。这就导致了在没有重叠时，梯度更新不能通过IoU损失直接进行。

为了解决这个问题，研究人员提出了一些IoU损失的变体，如：

- Smooth IoU Loss：通过添加一个小常数来平滑损失函数，确保梯度的存在。
- Generalized IoU (GIoU) Loss：GIoU损失考虑了边界框之间的距离，即使没有重叠也能提供非零梯度。
- Distance-IoU (DIoU) 和 Complete IoU (CIoU) Loss：这些都是进一步的改进，它们不仅考虑了边界框之间的重叠度，还考虑了它们之间的距离和长宽比。

这些变体确保了即使在没有交集的情况下，损失函数也是可导的，并且可以更有效地指导模型学习。

总结来说，IoU损失及其变体是优化目标检测和图像分割模型中边界框定位的有效方法。它们通过对预测和真实边界框之间的重叠度进行量化，直接影响模型的训练过程，并解决了原始IoU损失在梯度方面的问题。

# Dice Loss

Dice coefficient measures the overlap between the predicted mask and the ground truth mask.
$$
Dice(A, B) = \frac{2 \cdot |A \cap B|}{|A| + |B|}
$$
The Dice loss is then calculated as the complement of the Dice coefficient
$$
DiceLoss(A, B) = 1 - Dice(A, B)
$$
Dice Loss本身不可导，一般用作Eval的指标。但是如果一定要用来训练的话，可以使用smoothed version









