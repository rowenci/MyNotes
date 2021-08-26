# Faster R-CNN

## 原理

1. 将图像输入到网络中得到相应的特征图
2. 使用RPN(Region Proposal Network)来生成候选框，将RPN生成的候选框投影到特征图上获得相应的特征矩阵
3. 将每个特征矩阵通过ROI Pooling，缩放到7 * 7大小的特征图，接着将特征图flatten，并通过一系列FC层得到预测结果

