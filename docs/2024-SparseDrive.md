---
share: true
---
# Problem

- BEV dense的端到端计算量太大，影响规划安全；
- 之前的算法是先预测周围的运动，然后在进行本车的规划，整个过程是串行的，忽略了本车的运动可能对周边物体运动的影响；
- 在本车做规划的时候没有使用环境的语义信息；



# Concept

## Instance Representation
- 使用解耦的特征对象和几何表示；
- 最后整个环境是稀疏表述的；

## Symmetric Sparse Perception

## Parallel Motion Planner

- 同时做本车的运动规划和环境物体的运动预测，最后输出本车规划，



# Architecture

 ## Image Encoder
 - 通过一个backbone和neck提取出多角度，多尺度的图像特征$I$

## Detection

- 周围的每一个agent由instance feature和anchor box描述，参考[[2023-Sparse4d#Decoder|2023-Sparse4d > Decoder]]
- 使用类似[[2023-Sparse4d v2|2023-Sparse4d v2]]里的decoder和temporal fusion结构来更新输入的instance feature和anchor box；

## Online Mapping

- 结构和[[#Detection| > Detection]]一样，只是instance里的anchor box由polygon替换成map element；

## Tracking

- 使用[[2023-Sparse4d v3|2023-Sparse4d v3]]里的tracking方案；

## Ego Instance Initialization

- Ego的表示跟环境agent一样；
- 使用前视的最小的feature map来初始化ego feature；
- ego的box则


# Training



# Experiment



# Code

- https://github.com/swc-17/SparseDrive


# Ref
- SparseDrive End-to-End Autonomous Driving via Sparse Scene Representation.pdf
