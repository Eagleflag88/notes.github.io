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
	1. 对图片进行特征抽取（CNN）；
	2. Stereo matching with the previous frame image features;
	3. A plane-sweep volume in the reference view at 1/4 resolution，形成本帧的depth map
	4. 利用图片特征和depth map进行投影计算出本帧的BEV Feature；
- output：本帧的BEV Feature

## LOW-RESOLUTION, LONG-TERM OBJECT DETECTION TEMPORAL STEREO
- Input：
	1. BEV Map的序列，维持一个比较长的窗口，比如说16帧；
- Process
	- 把时间窗口之内的所有bev map进行Align and Concat
- Output
	- fused bev feature

## Head

- 在BEV Feature平面上用CenterHead做检测；