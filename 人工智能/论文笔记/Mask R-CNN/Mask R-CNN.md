# Mask R-CNN

Mask R-CNN在Faster R-CNN的基础上，添加了一个与BoundingBox Regression平行的处理分支。这个分支是用来预测一个对象掩码。

所以Mask R-CNN的作用就是用来做 instance segmentation

Instance segmentation is challenging because it requires **the correct detection**of all objects in an image while also **precisely segmenting each instance**.

