---
share: true
---
# Ref

- Original Paper: BEVFormer: Learning Bird’s-Eye-View Representation from Multi-Camera Images via Spatiotemporal Transformers.pdf

# Architecture

## Feature Extraction
For timestamp $t$, feature extraction using ResNet101 + FPN，每个视角产生一个feature map，维度是CxHxW。

## Temporal Attention
- 输入：
1. BEV Query：维度CxHxW, 这是一种学习出来的特征。此外，针对每个query还添加了可学习的positional embedding。
2. 历史BEV Feature，$B_{t-1}$维度256xHxW。
- Alignment：根据本车运动关系，把$B_{t-1}$加上本车运动，形成$B^{\prime}_{t-1}$
- Deformable Attention：在BEV Query中预测出几个reference point

## Spatial Attention
- 输入：BEV Query和每一个view的feature map。
- 采样：针对BEV Query的每一个点，按高度采样$N_{ref}$个点。然后利用相机内外参投影到各个view上。每个view形成若干个个投影点（不一定是$N_{ref}$）。这些投影点被称为reference point。
- Cross-Attention：然后我们计算reference point的feature和$Q_p$之间的attention。
- 输出：


