---
share: true
---
# Classfication

## Accuracy
- Top1 Accuracy: 通常指模型预测的最高置信度类别是否与真实类别匹配的准确性；
- Top5 Accuracy: 指的是模型预测的前五个最高置信度的类别中是否包含了真实的类别。换句话说，如果真实的类别出现在模型预测的置信度最高的前五个类别中的任何一个，那么这个预测就被认为是正确的;

## Precision（精度）
- 精度衡量的是预测为正类的样本中实际为正类的比例。计算公式为：

$$
P = \frac{TP}{TP + FP}
$$

- 其中，TP（True Positive）是真正类，FP（False Positive）是假正类；
- 最后两者相加就是模型认为是阳性的样本数量；

## Recall（召回）
- 召回率衡量的是实际为正类的样本中被正确预测为正类的比例。计算公式为：
$$
R = \frac{TP}{TP + FN}
$$
- 其中，FN（False Negative）是模型错误预测为负类的正类样本数。可以理解为漏检的样本数；
- 最后，两者相加就是所有实际类别为正的样本数量；

## PR 曲线

- 精度-召回曲线（Precision-Recall Curve，PR 曲线）是一条以召回率为横轴、精度为纵轴的曲线。通过改变决策阈值，可以得到不同的精度和召回率，即为PR曲线上的一个点。从而绘制出 PR 曲线；

## AP（平均精度）
- AP 是对 PR 曲线下的面积的近似计算。它衡量的是分类器在不同阈值下的整体性能。

## mAP
- 是多个类别的平均AP

# Object Detection

## AP（平均精度）

- 基本和分类任务用的AP一样，但是判定是否成功检测时使用的是IoU的阈值；

## F1-Score

- F1-score 是精度和召回率的调和平均数，综合考虑了精度和召回率。定义为：
$$
F1 = \frac{2P*R}{P + R}
$$

- 调和平均数对极端值更敏感，因此 F1-score 在 Precision 和 Recall 之间取平衡;
- 如果 Precision 或 Recall 中有一个较低，F1-score 也会较低;

# Segmentation

## Dice Coefficient

- Dice coefficient measures the overlap between the predicted mask and the ground truth mask.

$$
Dice(A, B) = \frac{2 \cdot |A \cap B|}{|A| + |B|}
$$

- The Dice loss is then calculated as the complement of the Dice coefficient

$$
DiceLoss(A, B) = 1 - Dice(A, B)
$$

- Dice Loss本身不可导，一般用作Eval的指标。但是如果一定要用来训练的话，可以使用smoothed version；

# Test

## Test-Time Augmentation
- Test-time augmentation, or TTA for short, is an application of data augmentation to the test dataset；
- Specifically, it involves creating multiple augmented copies of each image in the test set, having the model make a prediction for each, then returning an ensemble of those predictions；
- 训练好的模型对输入数据的变化（如旋转、缩放、亮度变化等）可能非常敏感。例如，一张稍微旋转的图像可能会导致模型预测错误，因为模型在训练时可能没有充分学习到这种变化的特征；
- 这种方式相当于让模型在推理阶段“看到”了更多的数据变化，从而减少了对单一输入的依赖，平滑了输出结果；







