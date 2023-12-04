---
share: true
---
# Classfication
- Top1 Accuracy: 通常指模型预测的最高置信度类别是否与真实类别匹配的准确性；
- Top5 Accuracy: 指的是模型预测的前五个最高置信度的类别中是否包含了真实的类别。换句话说，如果真实的类别出现在模型预测的置信度最高的前五个类别中的任何一个，那么这个预测就被认为是正确的;

# Test

## Test-Time Augmentation
- Test-time augmentation, or TTA for short, is an application of data augmentation to the test dataset.
- Specifically, it involves creating multiple augmented copies of each image in the test set, having the model make a prediction for each, then returning an ensemble of those predictions.







