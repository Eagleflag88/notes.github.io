---
share: true
---

# Problem

- Detr训练收敛太慢；
- Detr的小目标效果较差；
- 普通的Attention模块的注意力是平均的；

# Deformable Attention

## Concept
- 引入可变形的采样点来选择性地关注部分关键位置；
- Fast convergence, and computational and memory efficiency；
- 

## Architecture

- Input：
	1. Feature Map
	2. Query
	3. Reference Point：关键概念之一。它们用于定义查询位置的初始位置，并作为可变形采样点计算的基准。基本上就是均匀采样，我们就在这个的基础上加上offset得到最终的sampling point
- Process：
	1. query通过一个Linear线性层得到Offset；
	2. query通过一个Linear和Softmax得到Attention Weight。相当于Q和K的点乘结果；
	3. 通过Input feature map经过一个线性层，再通过reference point和offest选取特定位置的特征得到value；
- output：
	- 将Value与Attention Weight相乘后，经过一个线性层得到;

# Architecture

## Backbone

直接输出CNN(ResNet)的中间多个feature map；

## Pre-Transformer
对多层的feature map进行重整聚合，调整shape，最后形成bs, num_feat, num_embed的张量，feat_flatten, 作为encoder的输入。

## Transformer-Encoder

$\underline{作用}$
对feature map进行位置编码和self attention，通过自注意力聚合feature map全局的特征。

$\underline{Structure}$
(MsDeformableSelfAttention, LayerNormalization, FeedForwardNetwork, LayerNormalization)x6

$\underline{Process}$
1. 生成encoder使用的reference_point，基本上就是均匀采样；
2. 使用MultiScaleDeformableAttention的SelfAttention进行特征聚合，输入就是Q=feat_flatten+位置编码，Key和Value也是feat_flatten；重复6次，获得多尺度聚合的图片特征：bs, num_feat, num_embed，在mmdetection中被称为memory；

## Transformer-Decoder

$\underline{作用}$
1. 对object query进行self attention，使得每个object query关注不同的obejct信息;
2. 将object query与encoder_out进行cross attention，让object query在feature map上找到不同的obejct;

$\underline{Structure}$
- self attention(self_attention、normalization)
- cross attention(MSdeformable cross_attention、normalization、feed forward network、normalization)
- 上面两个结构组成一层，重复6层

$\underline{Process}$
1. 形成Object Query，shape是num_query, num_embed，这个是需要学习出来的；然后生成Query Pos，并利用它和一个线性变换生成参考点（DeformableAttention需要）；
2. SelfAttention：Object Query自己做自注意，这里用的是普通的MultiHeadAttention。
3. CrossAttention：用自注意的输出和Encoder的memory做互注意，企图获得我们要查找的object和图片的高维特征之间的关系；这个Attention机制中的Q是object query，K和V则是memory。因为要跟memory做交互，所以用了MSDeformableAttention。输出的shape跟Query是一致的；
4. 最后输出的是每一层的Object Query和参考点；

## Head

一次性预测N个object，然后使用匈牙利算法寻找和GT之间的最佳匹配，其中的cost包含BoundingBox和class loss
- Bounding Box loss：IoU + L1 loss
- Classification：negative log-likelihood

# Training

## Label Assignment Strategy
- 匈牙利算法寻找和GT之间的最佳匹配

# Ref 
- DEFORMABLE DETR: DEFORMABLE TRANSFORMERS FOR END-TO-END OBJECT DETECTION
- [详解](https://zhuanlan.zhihu.com/p/520666550)
- [DeformableDetr_CallGraph.xmind](https://1drv.ms/u/s!AoE-r_P7l4j3gv5X5XwlX-VIGStRjQ?e=He2fwW)

