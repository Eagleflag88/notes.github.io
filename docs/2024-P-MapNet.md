---
share: true
---

# Problem

- 构建静态环境的宝贵资源，sd map和hd map还没有用起来

# Process

- 使用Nuscene Argoverse数据集
- 利用openstreetmap生成sd map，然后对sd map进行栅格化

# Architecture

## BEV Feature Extraction

- Input
1. Camera
2. Lidar
- Process
1. 6 RGB → ResNet → Projection → Vision BEV Feature, $H \times W \times N_{cam}$
2. Lidar Point CLoud → PointPillarNet → Projection → Lidar BEV Feature $H \times W \times N_{lidar}$
3. Lidar + Vision的拼接 → 
- Output
1. BEV Feature $H \times W \times C$

## SD Prior Module
- Input
1. vehicle pose
2. global sd map

- Process
1. Retrieve the sourrounding sd map
2. 对sdmap进行平整化；
3. CNN on rasterized sd map
4. Sine positional embedding → $F_{sd}$
5. Cross multihead attetion with BEV Feature → 消除miss alignment

## HD Piror Module

## Segmentation Head and Losses
Same as [[2022-HDMapNet An Online HD Map Construction and Evaluation|2022-HDMapNet An Online HD Map Construction and Evaluation]]

# Training


# Experiment

- Conclusion
1. CNN Extraction of SDMap helps

## Conclusion

- 即使SDMap只有中心线，也能提高车道线和其他道路元素的检测性能；


# Code

- not yet published