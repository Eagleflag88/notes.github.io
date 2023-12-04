---
share: true
---

# Problem

- 现在的方法的过去帧考虑的时间太短，影响了深度的估计；
- 而且多帧融合考虑的都是bev feature层面，分辨率太低；

# Approach

- 定义一个metric，localization potential来描述某一个点的能够估计出来的深度随着source view的时间和分辨率的变化率；
	1. 对每个点的深度估计来说，没有一个最优的时间差；
	2.  Aggregating more timesteps can substantially increase the proportion of objects that can benefit from multi-view depth estimation，总的来说还是增加时间差比较有效；

- MVSNET
	- MVSNet predicts a probability for every (x, y, d) candidate location using the aggregated temporal information.
	- Intuitively, this probability value is a prediction of whether the candidate location is occupied in 3D space

# Architecture

## HIGH-RESOLUTION , SHORT-TERM DEPTH ESTIMATION TEMPORAL STEREO
- MVS temporal stereo pipeline using two consecutive frames
- Input: 连续两帧Image
- Process：
	1. Stereo matching with the previous frame image features，depth guided;
	2. A plane-sweep volume in the reference view at 1/4 resolution，形成本帧的depth map
	3. 输出本帧的BEV Feature 

## LOW-RESOLUTION, LONG-TERM OBJECT DETECTION TEMPORAL STEREO
- Input：
	1. 1/16 Feature Map，维持一个比较长的窗口，比如说16帧；
	2. 低分辨率Depth Map；
- Process
	- Align and Concat
- Output
	- fused bev feature
	