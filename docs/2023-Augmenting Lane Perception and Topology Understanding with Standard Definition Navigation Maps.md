---
share: true
---

# Problem

- HD Map太贵

# Concept

- 利用sd map来提升lane detection和topology reasoning的性能；
- 把sd map用transformer进行编码
- 输入gps，sdmap和多角度pv，输出中心线，traffic element和他们的连接性（通过affinity matrice）描述

## OSM
- road geometry
- road connectivity
- segment type: highway, residential roads, and pedestrian crossings

# Architecture

## SD Map Polyline Sequence Representation

- 用ego pose来retrieve local map
- 获得local map里面的polyline，每个polyline表示一个road segment
- 对polyline均匀采样，然后加上位置编码；
- 对每个点的类别one hot编码；
- 把类别编码和位置编码进行concat；

## Transformer Encoder

- Linear Projection
- L层的自注意力Transformer
- 输出：$M \times H$的feature，M是polyline数量，H是feature dimension

## Map Cross Attention

- 在Transformer的Encoder的每一次spatial attention之后和Map feature做cross attention

## Decoder

- 构建新的head输出中心线，traffic element和相关性

## Training

- 使用basemodel的训练框架
- 没有添加其他的监督信号


# Experiment

- 使用OpenLane V2数据集
- Base model：BEVFormer和TopoNet
- We set N = 11 for the polyline sequence representation of SD maps, d = 32 for the dimension of positional embeddings, and L = 6 layers for the SD map Transformer encoder;
- Road type: pedestrian, highway, residential, service, bus way, and truck road;

## Baseline Model

- baseline large: BEVFormer + DETR

## Conclusion

- 对远处的更加效果更好；
- 对intersection的效果更好；
- 使用transformer可以提升对topology的预测，而普通的encoding（raster + one hot）
-  Adding positional encoding is crucial for good topology reasoning

# Code

- https://github.com/NVlabs/SMERF.git