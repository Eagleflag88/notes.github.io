---
share: true
---

# Problem

- 之前的框架没有充分利用现存地图，即使是过时和不准确的地图
- 之前的地图匹配算法有些问题

# Concept

- 不精确的地图仍然有提升作用；
- 考虑下面三种场景来测试效果；
	1. 只有边界
	2. 地图不准确，米级误差；
	3. 地图变化
- 整个框架是基于MapTR的结构考虑的；

## Non-learning EX queries

- Input
1. 地图元素（矢量）

- Process
1. 对每个地图元素进行均匀采样，得到点；
2. 对每个点进行编码：坐标(x, y) + one-hot class labeling + Padding；
3. Concat：和MapTR的learnable queries进行拼接；

## Pre-Attribution

- 在训练的时候Matching之前加上；
- 只有预测和真值小于1m才进行matching；

# Architecture

- 在decoder的输入上加入ex queries
- 在训练matching阶段加入pre-attribution

# Experiment

- Nuscene Dataset上和MapTR比较；
- 自己开发了一个地图生成器来修改Nuscene的地图；

# Code

- Not yet published





