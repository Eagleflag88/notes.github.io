---
share: true
---

# Ref 
[详解](https://zhuanlan.zhihu.com/p/520666550)
[DeformableDetr_CallGraph.xmind](https://1drv.ms/u/s!AoE-r_P7l4j3gv5X5XwlX-VIGStRjQ?e=He2fwW)

# Deformable Attention

- 输入：
1. Feature map，维度CxHxW。
2. Query element: $z_q$。
3. Reference Point: $p_q$。
- 采样：在$p_q$点周维采样K个点，这K个点的偏移量（相对于参考点）和权重是通过一个MLP来生成的。权重生成后还会通过一个softmax来归一化。这个MLP是学习出来的。
- 权重求和。

1. Reference point是提前得到的，然后送入Deformable Attention模块直接使用；
2. Offest是输入query通过一个Linear线性层学习得到的；
3. Attention Weight是输入query通过一个Linear和Softmax学习得到的；
4. Value是通过Input feature map经过一个线性层后，再通过reference point和offest选取特定位置的特征得到的；
5. Output是将Value与Attention Weight相乘后，经过一个线性层得到的;


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
2. SelfAttention：Object Query自己做自注意，这个Object Query是待学习的，shape是num_query, query_embed；输出的维度也是一样；这里用的是普通的MultiHeadAttention。
3. CrossAttention：用自注意的输出和Encoder的memory做互注意，企图获得我们要查找的object和图片的高维特征之间的关系；这个Attention机制中的Q是object query，K和V则是memory。因为要跟memory做交互，所以用了MSDeformableAttention。输出的shape跟Query是一致的；
4. 最后输出的是每一层的Object Query和参考点；

## Head

一次性预测N个object，然后使用匈牙利算法寻找和GT之间的最佳匹配，其中的cost包含BoundingBox和class loss
- Bounding Box loss：IoU + L1 loss
- Classification：negative log-likelihood