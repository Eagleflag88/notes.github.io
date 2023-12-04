---
share: true
---
# Problem

- 现在基于纯视觉的方法的深度估计不准，而且要用很重的backbone；
- 一般的利用lidar的方法会显式的学习出一个深度，加大计算量，不利于车端部署；

# Approach

- Convert all features to the BEV space, maintaining both geometric structure and semantic information.
- Student Model：BEVFormer，a transformer-based image detector；
- Teacher Model：Object-DGCNN，a transformer-based LiDAR detector；

## DENSE FEATURE DISTILLATION

- 在2D和3D的BEV Feature上进行监督，而不是PV Feature；
- 在3D的BEV上根据是否有物体创造一个前景Mask；
- 在前景Mask上计算两者的Loss；

## SPARSE INSTANCE DISTILLATION
- 根据GT来筛选出可信度高的Instance；
- 计算2D和3D Instance之间的分类Loss与回归Loss；
- 利用Mutual Information软化Loss；