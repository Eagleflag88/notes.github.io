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

## Recall（召回）
- 召回率衡量的是实际为正类的样本中被正确预测为正类的比例。计算公式为：
$$
R = \frac{TP}{TP + FN}
$$
- 其中，FN（False Negative）是假负类；

## PR 曲线

- 精度-召回曲线（Precision-Recall Curve，PR 曲线）是一条以召回率为横轴、精度为纵轴的曲线。通过改变决策阈值，可以得到不同的精度和召回率，从而绘制出 PR 曲线；

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

# Test

## Test-Time Augmentation
- Test-time augmentation, or TTA for short, is an application of data augmentation to the test dataset.
- Specifically, it involves creating multiple augmented copies of each image in the test set, having the model make a prediction for each, then returning an ensemble of those predictions.







