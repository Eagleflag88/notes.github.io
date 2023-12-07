---
share: true
---

# Architecture

## Feature Extraction
- For timestamp $t$, feature extraction using ResNet101 + FPN，每个视角产生一个feature map，维度是CxHxW；
- 我们把各个视角的特征图stack到一起，然后进行flatten操作；

## Temporal Self-Attention
- 输入：
	1. BEV Query：维度H, W, num_query。这是一种学习出来的特征。此外，针对每个query还添加了可学习的positional embedding。
	2. 历史BEV Feature，$B_{t-1}$维度H, W, num_query。
- Process：
	1. Alignment：根据本车运动关系，把$B_{t-1}$加上本车运动，形成$B^{\prime}_{t-1}$
	2. Deformable Attention：把BEV Query当作Q，上一个时刻的BEV Feature当作K和V，我们做Deformable Attention。详细的过程请参考[[2021-Deformable Detr#Deformable Attention|2021-Deformable Detr > Deformable Attention]]；
	3. 我们实际上可以把BEV Feature理解为mmdetection框架下Transformer-Encoder的memory；
- Output：
	1. BEV Query和前一个时刻BEV Feature之间的相互关系，可以认为是临时的memory，也会被用作下一次Attention操作的Query；

## Spatial Cross-Attention
- 输入：
	1. BEV Query；
	2. 每一个view的feature map；
- Process：
	1. 投影：针对BEV Query的每一个点$Q_p$，按高度采样$N_{ref}$个点。然后利用相机内外参投影到各个view上。每个view内形成若干个投影点（不一定是$N_{ref}$）。这些投影点被称为参考点；
	2. 采样：然后我们会在这些参考点周围进行采样获得特征向量$F$；
	3. Cross-Attention：然后我们计算$Q_p$和$F$之间的attention；
- 输出：
	- 包含了原始视角和BEV视角的关系的BEV Feature；

## Head
- 上面的Temporal Attention，Spatial Attention再加上中间的LayerNorm和FFN实际上形成了Tranformer的Encoder结构，输出的是memory；
- 它可以作为Decoder的输入参与各种后续任务，不管是检测还是分割；

## Implementation

- Feature Extraction：ResNet101-DCN/VoVnet-99；
- Neck：FPN；
- BEV Feature：200x200x256；
- Encoder：6 Layers的相同结构堆叠；

# Ref

- BEVFormer: Learning Bird’s-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers


