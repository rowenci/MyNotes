# Faster R-CNN

![aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMDEyMTE0OTIxNjU5](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcxMDEyMTE0OTIxNjU5.jpg)

## 原理

1. 将图像输入到网络中得到相应的特征图
2. 使用RPN(Region Proposal Network)来生成候选框，将RPN生成的候选框投影到特征图上获得相应的特征矩阵
3. 将每个特征矩阵通过ROI Pooling，缩放到7 * 7大小的特征图，接着将特征图flatten，并通过一系列FC层得到预测结果

## 区别

1. 非端到端
2. 使用RPN来解决selective search拉低速度的问题（CPU上运行的聚类算法）

