---
share: true
---
# Problem

- 非端到端的方法信息利用和传递效率低下；
- Dense BEV-Centric的端到端方法计算量太大（因为需要进行时空融合），而且性能比不过单任务模型；


# Concept

## Sparse Query-Centric
- 所有的元素通过通过sparse query表示；
- 在时空融合和多模态输入融合方面更有效率；

## Sparse Perception

- 一次性处理检测和跟踪的任务，包含动态和静态；
- 具体来说，多传感器的feature输入和暂存的memory用token表示；
- Object Query和map query表示障碍物和地图元素；

## Motion Planner

- 首先我们预测本车和其他agent的运动，以获得本车的初始规划；
- 综合考虑运动约束获得最后的规划；

# Architecture

##  Sensor encoders

- Image, radar和lidar的原始数据编码成高维feature；

## Sparse perception

- 把原始输入feature累积成Query；

## Detection&Tracking

- 

## Online Mapping

- 在驾驶平面均匀的初始化一些map query；
- 把map query和历史帧的map query用attention做decode。生成本帧的map query，存入memory bank；
- Bezier Map Head 输出地图元素；

## Motion planner

- 




# Training



# Experiment



# Code
- 无

# Ref

- SparseAD Sparse Query-Centric Paradigm for Efficient End-to-End Autonomous Driving
