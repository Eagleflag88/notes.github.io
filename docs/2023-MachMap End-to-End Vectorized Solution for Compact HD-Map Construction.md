---
share: true
---
# Concept
- 保持元素内部点方向的延续性。
- 使用时序信息。
- Preprocess：对GT数据进行了预处理，主要统一了方向的表达，然后利用Douglas-Peucker算法对元素进行了稀疏化。

# Architecture
- Feature Extraction: InternImage（强力方法） + BiFPN -> Camera Feature;
- Multiview-Encoder：在BEV平面定几个参考点 → 利用相机内外惨投影到相机平面 → 在参考点附近进行特征聚合，然后在z轴进行平均 → 输出BEV Feature（HbxWbxC）。
- Temporal Fusion Module：a. 上一个时刻的隐藏状态的BEV Feature + ego pose的变换 → 本时刻的BEV Feature的估计状态 + BEV的观测状态（利用现在的图片生成的）→ 这个时刻的隐藏状态的BEV Feature。实际上，隐藏状态的BEV才参与后续计算，算是估计值的概念。
- Instance Decoder: Query包含content and position vectors。Content Vector生成 instance masks。Position Vector生成地图元素的坐标。使用 instance-level deformable attention。Query的组织方式参考了MapTR
- Output Head：路沿→ 回归头。人行道和车道线用分割头，参考Mask DINO？
- 后处理：利用mask的输出thin掉各个地图元素

# Training
- L1 Loss for Regression.
- Cross-entropy and dice-loss for Mask

# Sensor
周视

# Code
https://github.com/hustvl/MapTR.git