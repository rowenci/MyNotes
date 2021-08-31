# Faster R-CNN

![Faster](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\Faster.jpg)

![Faster](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\Faster.png)

## 1. 原理

1. 将图像输入到网络中得到相应的特征图
2. 使用RPN(Region Proposal Network)来生成候选框，将RPN生成的候选框投影到特征图上获得相应的特征矩阵
3. 将每个特征矩阵通过ROI Pooling，缩放到7 * 7大小的特征图，接着将特征图flatten，并通过一系列FC层得到预测结果

## 2. 结构

1. conv layer：是一个卷积神经网络，用于提取原始图片的特征。
2. RPN(Region Proposal Network)：RPN是Faster R-CNN中最重要的部分。也是与Fast R-CNN区别最大的部分。RPN是用来生成候选框的，作用与R-CNN和Fast R-CNN中的selective search算法相同。
3. ROI Pooling：用于resize特征图。
4. Classification, Regression：使用RPN生成的候选框来分类，并对候选框进行BoundingBox Regression。

## 3. 详解

### 3.1. Conv layer

在Faster R-CNN中，backbone采用的是VGG-16。

![vgg16](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\vgg16.png)

一个MxN大小的矩阵经过Conv layers固定变为(M/16)*(N/16)。这样Conv layers生成的featuure map中都可以和原图对应起来。方便在后面进行候选框在特征图上的投影。

### 3.2. RPN

这是Faster R-CNN中最重要，也是最有亮点的一部分。先说一下RPN的作用，与R-CNN和Faster R-CNN相同，就是用来生成候选框的。但是RPN能够极大提升候选框的生成速度。

下面是RPN网络的结构

![RPN](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\RPN.png)

#### 3.2.1. Anchor

anchor是RPN独创的一个生成候选框的机制。

![1](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\1.png)

假设在conv layer之后，会输出256个通道的特征图，那么，再进行padding=1， 卷积核为3的卷积操作之后，通道还是256个，但是特征图中每一个元素又都会融合周围3 * 3的空间信息。

之后，分成两条路径，这两条路径虽然都是采用1 * 1卷积进行运算，但是作用却完全不相同。

左边的绿色箭头是用来对每个anchor的foreground和background进行评分。也就是对这个anchor是否包含被检测物体进行评分。因为有k个anchor，所以要有2k个score。这2k个score会在后面用来进行softmax分类。

右边的绿色箭头是用来对anchor进行BoundingBox Regression的，分别对应[x, y, w, h]这4个偏移量。所以会生成4k个coordinate。用来进行BoundingBox Regression。

**注：anchor并不是在特征图上生成的，而是在原图上生成的。原图生成anchor之后**



### 3.3. ROI Pooling

![2](F:\MyNotes\人工智能\论文笔记\Faster R-CNN\2.gif)

1. 根据输入image，将ROI映射到feature map对应位置；
2. 将映射后的区域划分为相同大小的sections（sections数量与输出的维度相同）；
3. 对每个sections进行max pooling操作；

首先将region proposal映射到经过CNN处理的feature map上

然后对映射后的区域进行划分，如果需要输出一个2 * 2的特征图，就分别对region proposal的宽和高进行除2操作。比如上图的region proposal是7 * 5大小，那么第一个section就是7 / 2下取整就是3, 5 / 2 = 2。剩下的就是其余的section。等section划分完成后就对每个section进行max Pooling操作。

### 3.4. Classification, Regression



## 4. 区别

1. 端到端
2. 使用RPN来解决selective search拉低速度的问题（CPU上运行的聚类算法）

