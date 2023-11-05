---
share: true
---
# Ref
- [保姆级transformer解释](https://wangguisen.blog.csdn.net/article/details/125074022?spm=1001.2014.3001.5502)
- [Positional Encoding](https://www.zhihu.com/question/347678607)
- [Attention详解](https://blog.csdn.net/qq_42363032/article/details/124651978)
# Embedding

# Positional Encoding

## 现有方法及其缺点
- 方法一：直接用token的index，会有数值上的问题；
- 方法二：Normalized过后的index，不同长度的序列的步长是不一致的；
- 方法三：利用二进制向量表述位置信息，离散的向量，无法训练；

好的方法需要满足**有界**且**连续**的。

## 解决方案
- 假设t是这个token在序列中的实际位置（例如第一个token为1，第二个token为2...）；  
-  定义$PE_t$是是这个token的位置向量，长度为d的向量， $PE_{t}^{(i)}$ 表示这个位置向量里的第i个元素；
- $d_{model}$ 是这个token的维度（在论文中，是512)；

则$PE_{t}^{(i)}$可以表示为：  
$$
PE_{t}^{(i)}=sin⁡(w_it),\ if\ k=2i 
$$
$$
PE_{t}^{(i)}=cos⁡(w_it),\ if\ k=2i+1 
$$

这里：  
$$ w_i = \frac{1}{10000^{2i/d_{model}}} $$   
$$ i = 0,1,2,3,...,\frac{d_{model}}{2} -1 $$

## 性质
- 两个位置编码$PE_{t}^{(i)}$和$PE_{t}^{(j)}$的点积(dot product)仅取决于偏移量，也即两个位置编码的点积可以反应出两个位置编码间的距离；
- 位置编码的点积是无向的；

## Relative Potisional Embedding
上面介绍的都是按照token绝对位置编码的方法，事实上，我们也可以利用token之间的相对位置进行位置编码，大概步骤如下：
1. 首先计算token之间的相对位置。例如，如果词 A 在词 B 的前面，且两者之间有3个词，那么相对位置就是 -3；
2. Relative Positional Embedding：为每个可能的相对位置分配一个嵌入向量。这些嵌入可以是可学习的，或者根据某种预定义的函数生成；
3. 嵌入应用：将相对位置嵌入应用于模型的输入。通常，这些嵌入会与词汇的嵌入向量一起被输入到模型中，可以通过加法或拼接（concatenation）的方式结合它们；

## Relative Position Bias
- Relative Position Bias旨在通过向注意力机制引入偏差来帮助模型偏向于关注与当前位置相对位置特定的其他位置；
- 通常通过在注意力分数计算中添加一个可学习的偏差项$b$来实现，该偏差项基于元素之间的相对位置。公式如下：

$$ 
Attention(Q,K,V) = \sum^L (a + b)V
$$


# Attention

## Concept
1. 从众多关注点中找到最重要的关注点。
2. 聚焦的过程体现在**权重系数**的计算上，权重越大越聚焦于其对应的**value**值上即权重代表了信息的重要性，而**value**是其对应的信息。
3. 比如在翻译一个句子的某个词的时候，我们不应该把注意力平均放到所有的输入上面，而是应该聚焦到某个输入，思考他应该如何被翻译。而具体应该聚焦到输入的哪个部分则是训练之后学习到的，体现在Query？最后学习出来的是什么？

## Process

$\underline{输入}$
- K和V：键值对。
- Q：Query，指询问，是系统实际的输入。

$\underline{计算过程}$
1. 计算Q对每个K的相似性，可以用向量点积（$K*Q$），或者其他任何相似性的表达方式。其结果是注意力得分$S$；
2. 对这一批得分进行softmax操作，归一化，获得一个概率分布，描述的是每个K的权重系数$a$；
3. 根据权重系数对V进行加权求和，即可求出Query在现在这个K-Q组合的Attention数值：
$$ 
Attention(Q,K,V) = \sum^L aV
$$

## Padding Mask
在Transformer模型中，`padding_mask`或者`mask_padding`是用来处理变长序列的机制。由于神经网络通常需要固定长度的输入，但实际应用中的序列数据往往是变长的，因此通常会通过填充（padding）的方式来使所有序列达到同一长度。但是，填充的部分不应该对模型的计算产生影响，这就需要一种机制来告诉模型哪些位置是填充的，应该被忽略。这种机制就是通过`padding_mask`来实现的。
`padding_mask`是一个与输入序列同形状的布尔掩码（Boolean mask），在该掩码中，填充位置的值为`True`，而非填充位置的值为`False`

# Self-Attention

一种特殊的Attention机制，$Q$和$K$是同一个向量。

$\underline{输入}$
- X: 维度是LxD。L是token的个数，D是一个超参数，可以定为768。实际上是输入进行embedding之后的结果。
- $W_Q$,$W_K$和$W_V$：维度是DxD，用来对X进行线性变换，可学习的参数。

$\underline{计算过程}$
1. 对X进行线性变换，如下：
$$ 
Q = W_QX
$$
$$ 
K = W_KX
$$
$$ 
V = W_VX
$$
获得，Q，K和V，他们都是LxD维的。Q是我们正要查询的信息，K是正在被查询的信息，V是被查询到的内容
2. 计算Q和K的相似性，注意，这里的计算具体来说是在$Q_i$和$K_i$，$V_i$之间进行的。i代表第i行。每一行都代表一个token的相关向量，Q，K和V。然后利用softmax进行归一化，获得注意力权重$A$，维度是LxL。
3. 计算注意力：Z = A*V，维度是LxD。

# Multi-Head Attention
- 多头注意力机制，是在自注意力的基础上，使用多种变换生成的Q、K、V进行计算，再将它们对相关性的结论综合起来，进一步增强自注意力的效果。
- **多头指的就是多套Q、K、V**。比如论文原文是8个头，那就是8套Q、K、V。
- **经过注意力之后的矩阵会有自己理解的语义信息，那所以最后8个Z就会有8个不同的理解**。
- 多头信息输出，由于多套参数得到了多个信息，然而我们还是只需要一个信息，因此可以通过某种方法(例如矩阵相乘)把多个信息汇总为一个信息。
- 实际操作时，会使用同一套$W_Q$,$W_K$和$W_V$，然后把Q，K和V也切成几个部分。

# Layer Normalization
与BatchNorm：在一个Batch之内包含若干个sample，每个sample有若干个feature，BN是在feature层面进行归一化，LayerNorm是在每个sample内部跨feature做归一话。

# Feed-forward Network
两层的全连接。通过激活函数引入非线性变换，变换了Attention output的空间, 从而增加了模型的表现能力。

# Decoder
由两个部分组成：
1. Masked Multi-Head Attention
2. Encoder-Decoder Multi-Head Attention

