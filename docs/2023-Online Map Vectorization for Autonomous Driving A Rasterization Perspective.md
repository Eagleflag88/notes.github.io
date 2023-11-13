---
share: true
---

# Idea
使用IoU（基于rasterization + dilation）而不是chamfer-distance作为eval的指标

# Training
- Vector Model（MapTR） → Differentiable Rasterization → pixel-wise检测结果，instance+label
- Training：GT → Differentiable Rasterization
- Loss
	1. Segmentation Loss:  Dice Loss
	2. Direction Loss: 尽量减少方向的变化，让结果更smooth

# Inference
不需要rasterization
