---
share: true
---

# Ref

Original Paper: FCOS Fully Convolutional One-Stage Object Detection.pdf
[FCOS_Callgraph](https://1drv.ms/u/s!AoE-r_P7l4j3g4U_qa2MiCnoafTX-Q?e=mvDtJk)

# Problem

Anchor-based Detector有几个问题：
1. Detection performance is sensitive to the sizes, aspect ratios and number of anchor boxes；
2. Anchor的scale和aspect ratio是固定的，灵活性较低；
3. Anchor-based Detector一般需要生成很多anchor boxes，在训练之中，大部分都会被标注为负样本，造成样本不平衡，影响训练效果；

# Approach

- 利用Fully Convolutional Network，不依赖于任何全连接层来预测目标的边界框和类别，而是通过卷积层直接在特征图上进行预测；
- 不使用锚框（anchor boxes）来预测目标，直接在特征图的每个位置预测目标的存在和属性，包括边界框的四个偏移量和类别概率；
- FCOS在特征图上为每个像素位置编码一个中心度（centerness）得分，用来衡量一个像素点作为目标框中心的可能性；
- 总体来说，we reformulate object detection in a per-pixel prediction fashion；  

# Architecture  

## Backbone + Neck

- ResNet + FPN

## Head

- 对每一层的feature map我们先用4个卷积层进行卷积操作；
- 对卷积之后输出的feature map上的pixel我们预测他的：
    1. class
    2. BBox的4个点
    3. Centerness  

# Training

## BBox Target

1. 对任意一层feature map上的某个像素点，我们可以通过卷积升降维的关系找到他在输入图片上的像素点$p_{in}$；
2. 如果$p_{in}$落在一个正样本BBox之中，则我们可以直接获得他的类别的BBox；
3. 如果$p_{in}$落在多个正样本BBoxes之中，则我们选择面积最小的BBox作为Target；
4. 通过对每一层的feature map的object的大小做限制，解决重叠区域object分配的问题；

## Loss

- Cls_loss: Focal Loss；
- Reg_loss:  IoU Loss，可导版本；
- Centerness_loss：Binary Class Entropy，因为实际上是一个二分类问题；

对特征图上的所有像素点都计算他的Loss，并求平均。

# Inference

1. Forward the input image through the network and obtain the predicted bounding boxes with a predicted class。
2. 移除低分辨率检测：去掉置信度分数低于某个阈值的检测结果。
3. 应用中心度分数：用预测的中心度分数调整置信度分数，以降低边缘部分的检测框的得分。
4. 解码边界框：将预测的边界框偏移量转换回原始图像的尺寸。
5. 非极大值抑制（NMS）：虽然FCOS旨在减少对NMS的依赖，但在实际应用中，通常仍会使用NMS来移除重叠的检测框，保留最佳的检测结果。