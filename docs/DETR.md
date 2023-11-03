---
share: true
---

# Process

Architecture:
Backbone: Image(3xH0xW0) -> CNN(ResNet) -> Feature Map (CxHxW, 2048xH0/32xW0/32) -> Conv1x1 -> dxHxW(d一般比C小) -> Flatten -> dxHW 
Transformer-Encoder:
Input: Img_Feature(dxHW) + PE(dxHW)
Process: 
1 MultiHeadSelfAttention: 
Q = Img_Feature(dxHW) + PE(dxHW), 
Key = Img_Feature(dxHW) + PE(dxHW); 
V =  Img_Feature(dxHW)
Output: Query(dxHW)
2 Add + LayerNorm → Output: Query(dxHW)
3 FNN → Output: Query(dxHW)
4 Add + LayerNorm → Output: Query(dxHW)
Encoder Output: Query(dxHW)
这个Encoder的结构会重复6次；

Transformer-Decoder:
Input: Encoder-Out(dxHW), Object-Query(num_queryxdim_query)→ (100x256)
Process:
1 第一部分是对object query进行self attention，使得每个object query关注不同的obejct信息
Self-attention:
a 初始化object query [num_layer(6), num_query(100), B, 256]
b key、value：在self attention中，k和v是由q计算得到，None
c query_positional_embedding： query的位置编码 [num_layer(6), num_query(100), B, 256]
Output: 经过自注意力的object query [num_layer(6), num_query(100), B, 256]
2 第二部分是将object query与encoder_out进行cross attention，让object query在feature map上找到不同的obejct:
query：经过自注意力得到的object query，即self attention模块的输出 [num_layer(6), num_query(100), B, 256]
key、value：cross attention中，k和v都是encoder的输出 [N, B, 256]
query_positional_embedding： query的位置编码 [num_layer(6), num_query(100), B, 256]
key_positional_embedding：key的位置编码,即feature map的位置编码 [W_feat*H_feat, B, 256]
key_padding_mask：key，即feature map的有效padding mask [B, W_feat*H_feat]

## Ref
[代码解析]{https://zhuanlan.zhihu.com/p/348060767}

## Feature Extraction
ResNet-50，输出维度CxHxW

## Transformer encoder
- 输入：feature map, CxHxW
- Flatten: 使用1x1卷积把feature map平展成$z_0$,其维度是dxHxW，然后我们把$z_0$降维，获得一个输入序列，维度是dxHW。
- Encoder：把输入序列加上位置编码之后输入到普通transformer之中，做multi-head self attention，重复6次
- 输出: dxHW

## Decoder
- 输入：Object Queries, 实际上是学习出来的postional embedding，维度是Nx265
- Multi-Head Self Attention: 输入是Object Queries
- Multi-Head Cross Attention: 上面的输出和Encoder输出
- FFN：输出每个Object的bounding box和class


## Inference
一次性预测N个object，然后使用匈牙利算法寻找和GT之间的最佳匹配，其中的cost包含BoundingBox和class loss
- Bounding Box loss：IoU + L1 loss
- Classification：negative log-likelihood



# Deformable Detr


## Deformable Attention
- 输入：
1. Feature map，维度CxHxW。
2. Query element: $z_q$。
3. Reference Point: $p_q$。
- 采样：在$p_q$点周维采样K个点，这K个点的偏移量（相对于参考点）和权重是通过一个MLP来生成的。权重生成后还会通过一个softmax来归一化。这个MLP是学习出来的。
- 权重求和。

## Architecture

### Backbone

直接输出CNN(ResNet)的中间多个feature map；

### Pre-Transformer
对多层的feature map进行重整聚合，调整shape，最后形成bs, num_query, num_embed的张量，feat_flatten, 作为encoder的输入。

### Transformer-Encoder

$\underline{作用}$
对feature map进行位置编码和self attention，通过自注意力聚合feature map全局的特征。


$\underline{Process}$
1. 生成encoder使用的reference_point，基本上就是均匀采样；
2. 使用MultiScaleDeformableAttention的SelfAttention进行特征聚合，输入就是Q=feat_flatten+位置编码，Key和Value也是feat_flatten；重复6次，获得多尺度聚合的图片特征：bs, num_query, num_embed，在mmdetection中被称为memory；

### Transformer-Decoder

$\underline{作用}$
1. 对object query进行self attention，使得每个object query关注不同的obejct信息;
2. 将object query与encoder_out进行cross attention，让object query在feature map上找到不同的obejct;

$\underline{Process}$
1. 
