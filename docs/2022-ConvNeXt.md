---
share: true
---

# Problem

- 现在的CNN无法挑战Transformer作为一个通用的特征抽取网络；

# Approach

- 以ResNet为基础，构建一个纯CNN的能够跟Transformer比肩的网络；

## Training Techniques
- The training is extended to 300 epochs from the original 90 epochs for ResNets;
- AdamW optimizer;
- Data augmentation: Mixup, Cutmix, RandAugment, Random Erasing；
- Regularization schemes: Stochastic Depth and Label Smoothing;
- +2.7%

## Marco Design

- Adjust the number of blocks in each stage from (3, 4, 6, 3) in ResNet-50 to (3, 3, 9, 3), +0.6%;
- “Patchify stem”: +0.1%;
- Use depthwise convolution and wider convolution, + 1.0%;
- Inverted Bottleneck: 在每个Block中实施，减少计算量，稍微提高performance，+0.1；
- Large Kernel Sizes：模仿Transformer中的MSA拥有的Global感受野；

## Micro Design
- Replacing ReLU with GELU：brings no gain;
- Less Activation and BN Layer：+0.8%；
- Substituting BN with LN：+0.1%
- Separate downsampling layers:原来的ResNet直接在一开始就降采样了，+0.5%； 

# Ref
- Original Paper: A ConvNet for the 2020s

