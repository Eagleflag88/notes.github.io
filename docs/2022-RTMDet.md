---
share: true
---

# Problem
- 之前的检测器的backbone和neck的结构不够高效；
- 在训练过程中，传统的target匹配方法比如说基于IoU的方法不够好；
- 在目标匹配的时候传统方法的匹配函数和最终的训练Loss函数一般是一致的，我们认为这个不够好；

# Approach

- Large-kernel depth-wise convolutions；
- Better optimization with soft labels in the dynamic label assignments； 

# Architecture  

The macro architecture of RTMDet is a typical one-stage object detector

## Basic Building Block

- 基本思路：One can enlarge the receptive field with a reasonable computational cost through depth-wise convolution；
- 结构：
	1. 3x3 Conv + BN + SiLU，SiLU详见[[Normalization#Activation|Normalization > Activation]]
	2. 5x5 Depthwise Conv；
	3. 1x1 Pointwise Conv；
	4. BN + SiLU；

## Backbone

- 在CSPDarkNet的基础上进行修改。整体的结构包含若干一个Stem层和若干个Stages（一般是4个）。每个Stage包含若干个Basic Building Block；
- 为了减少Basic Building Block中Pointwise Conv对模型推理并行度的影响，我们会减少Basic Building Block的数量，同时增加Basic Building Block的宽度；

## Neck

The neck takes the multi-scale feature pyramid from the backbone and uses the same basic building blocks as the backbone with bottom-up and top-down feature propogation；
- 接收Stage 2，3，4的输出，进行从上到下和从下到上两轮拼接，卷积操作；
- 最后得到3个Scale的特征图，他们的长宽不一样，但是通道数一致；

## Head

- 使用类似与FCOS的机制，在各个尺度上共享预测头的参数；
- 但在不同的Scale上使用不同参数的BN层；
- Process：
	1. 把3个特征图作为输入，先使用Conv + BN + SiLU进行卷积，这里的参数是各个尺度不一样的；
	2. 使用两个单独卷积层，分别输出BBox和Cls，这里的参数是各个尺度是一样的；

# Training

## Data Augumentation

RTMDet 采用了多种数据增强的方式来增加模型的性能，主要包括单图数据增强:

- RandomResize 随机尺度变换
- RandomCrop 随机裁剪
- HSVRandomAug 颜色空间增强 
- RandomFlip 随机水平翻转
    

以及混合类数据增强：

- Mosaic 马赛克
- MixUp 图像混合

## Label Assignment Strategy

- 动态分配策略：使用预测的Bboxes与GT的IOU和分类分数拿来计算Matching Cost矩阵，再通过 top-k 的方式动态决定样本选取以及样本个数；
- 匹配的Cost分三个部分，但是是soft版本的：
	 1. Classification；
	 2. Region Prior；
	 3. Centerness；
- 通过计算上述三个损失的和得到最终的 cost_matrix 后, 再使用 SimOTA 决定每一个 GT 匹配的样本的个数并决定最终的样本；


## Loss

- Cls_loss: Quality Focal Loss；
- Reg_loss:  GIoU Loss；

# Inference

1. 特征图输入

预测的图片输入大小为 640 x 640, 通道数为 3 ,经过 CSPNeXt, CSPNeXtPAFPN 层的 8 倍、16 倍、32 倍下采样得到 80 x 80, 40 x 40, 20 x 20 三个尺寸的特征图。以 rtmdet-l 模型为例，此时三层通道数都为 256，经过 `bbox_head` 层得到两个分支，分别为 `rtm_cls` 类别预测分支，将通道数从 256 变为 80，80 对应所有类别数量; `rtm_reg` 边框回归分支将通道数从 256 变为 4，4 代表框的坐标；

2. 初始化Anchor

根据特征图尺寸初始化三个网格，大小分别为 6400 (80 x 80)、1600 (40 x 40)、400 (20 x 20)，如第一个层 shape 为 torch.Size([ 6400, 2 ])，最后一个维度是 2，为网格点的横纵坐标，而 6400 表示当前特征层的网格点数量。这里其实就是Anchor的初始化；

3. 阈值过滤

先使用一个 `nms_pre` 操作，先过滤大部分置信度比较低的预测结果（比如 `score_thr` 阈值设置为 0.05，则去除当前预测置信度低于 0.05 的结果），然后得到 bbox 坐标、所在网格的坐标、置信度、标签的信息。经过三个特征层遍历之后，分别整合这三个层得到的的四个信息放入 results 列表中。

4. 还原到原图尺度

最后将网络的预测结果映射到整图当中，得到 bbox 在整图中的坐标值

5. NMS

进行 nms 操作，最终预测得到的返回值为经过后处理的每张图片的检测结果，包含分类置信度，框的 labels，框的四个坐标

# Ref

- RTMDet: An Empirical Study of Designing Real-Time Object Detectors
- [原理和结构详解](https://mmyolo.readthedocs.io/zh-cn/latest/recommended_topics/algorithm_descriptions/rtmdet_description.html)