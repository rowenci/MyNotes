# Machine Learning

Optimizing Algorithm is very import to Machine Learning. In a Machine learning problem, we usually define a loss function first. And then we can minimize this loss function using the optimizing algorithm.

## 1. Linear Regression

线性回归是通过多项式来拟合数据分布，并以此来进行预测。

### 1.1. Hypothesis Function

$h(x) = \theta_0 + \theta_1 x$ 是一个问题的假设函数，它表示了这个问题的模型。

根据输入$x$和两个参数$\theta_0$，$\theta_1$来计算出这个样本对应的预测值$h(x)$。

在刚开始，这个模型当中的参数$\theta_0$，$\theta_1$均为随机初始化的两个数，这两个参数会在之后的训练当中被不断优化，直到符合数据集。

### 1.2. Loss Function

purpose : 用来对当前模型所预测出的数据与实际数据作出误差判断。

$J(\theta_0, \theta_1)=\frac{1}{2m}\sum_{i=1}^m[h_\theta(x^{(i)}-y^{(i)})]^2$

上式是这个问题的loss function，平方差求和函数。它计算的是在参数（$\theta_0$，$\theta_1$）下的hypothesis function$h(\theta)$与实际的训练集的误差。

### 1.3. Optimizer

使用Gradient Descent来最小化Loss Function

purpose : 用来对参数进行更新

repeat until converge{

​	$\theta_j = \theta_j - \alpha \frac{\partial}{\partial\theta}J(\theta_0, \theta_1)$

​	(for j = 0 and j = 1)

}**simultaneously update $\theta_0$ and $\theta_1$**

其中$\alpha$为Learning Rate，它决定了梯度下降中每个step的步长是多少

由算法可知，$\theta_j$每一个step都是减去$\alpha$与$J(\theta_0, \theta_1)$在该点处的导数（即斜率）。

那么为啥要减去导数呢？

因为你去导数的话，就无需对梯度下降的方向进行判断了，它会自动决定是加还是减。

## 2. Classification Problems

### 2.1. Logistic Regression

#### 2.1.1. Hypothesis Function

$h_\theta(x)=g(\theta^Tx)$

$g(z)=\frac{1}{1+e^{-z}}$

由上面两个式子可得logistic回归的假设函数为

$h_\theta(x)=\frac{1}{1+e^{-\theta^Tx}}$

$h_\theta(x)$是用来在输入$x$的情况下，得到$y=1$的概率情况

因为logistic函数范围是0~1，因此符合概率的要求。

#### 2.2.2. Loss Function

$J(\theta)=\frac{1}{m}\sum_{i=1}^{m}cost(h_\theta(x^{(i)}),y^{(i)})$

$cost(h_\theta(x),y)=-log(h_\theta(x))$ if $y=1$​

$cost(h_\theta(x),y)=-log(1-h_\theta(x))$ if $y=0$

由上式可将两种情况合并起来得到新的损失函数

$J(\theta)=-\frac{1}{m}\sum_{i=1}^m[y^{(i)}log(h_\theta x^{(i)})+(1-y^{(i)})log(1-h_\theta(x^{(i)}))]$

#### 2.2.3. Optimizer

使用梯度下降算法

repeat until converge{

$\theta_j = \theta_j - \alpha \frac{\partial}{\partial\theta_j}J(\theta)$​​

​     $=\theta_j-\alpha\frac{1}{m}\sum_{i=1}^{m}[(h_{\theta}-y^{i})x_j^{(i)}]$​​​​​

(for j = 0 and j = 1)

}**simultaneously update all parameters**

注：在求导时，$g^\prime(z)=g(z)(1-g(z))$

在这个梯度下降中，因为是对$\theta$求导，所以后面要加上$\frac{\partial}{\partial\theta}(\theta^Tx)$

### 2.2. SoftMax Regression

softmax回归和logistic的区别就是，一个logistic函数只能进行一个二分类问题

而softmax函数可以进行多分类

softmax函数其实就是pytorch里面的交叉熵损失函数，因为计算和损失函数分开计算的话效果很不好所以pytorch就直接给他俩合成一个损失函数了。

#### 2.2.1. Hypothesis Function

假设该分类有4个feature，3个输出

$o_1=x_1w_{11}+x_2w_{21}+x_3w_{31}+x_4w_{41}+b_1$

$o_2=x_1w_{12}+x_2w_{22}+x_3w_{32}+x_4w_{42}+b_2$​

$o_3=x_1w_{13}+x_2w_{23}+x_3w_{33}+x_4w_{43}+b_3$​

这个其实就是一个全连接层，下面的计算在pytorch中与损失函数合到一起了。

$\hat{y_1},\hat{y_2},\hat{y_3}=$softmax$(o_1,o_2,o_3)$

其中

$\hat{y_1}=\frac{e^{o_1}}{\sum_{i=1}^{3}e^{o_i}}$

$\hat{y_2}=\frac{e^{o_2}}{\sum_{i=1}^{3}e^{o_i}}$​

$\hat{y_3}=\frac{e^{o_3}}{\sum_{i=1}^{3}e^{o_i}}$​

#### 2.2.2. Loss Function

cross entropy

$H(y^{(i)},\hat{y}^{(i)})=-\sum_{j=1}^{q}y_j^{(i)}log\hat{y}_j^{(i)}$

$l(\Theta)=\frac{1}{n}\sum_{i=1}^{n}H(y^{(i)},\hat{y}^{(i)})$​​

#### 2.2.3. Optimizer

gradient descent

## 4. Tricks

How to make training fast and robust.

### 4.1. Feature Scaling

purpose : make sure features are on a similar scale.

如果不同特征之间数值范围相差很大的话，画出梯度的等高线之后会发现整个等高图会变得细长，那么不利于梯度向中心移动。而假如不同特征值之间数值范围相同，那么等高图会变成接近一个圆。而圆上的点的梯度方向都是圆心，所以梯度下降速度会变快。

solution : get every feature into approximately $-1 \le x \le 1$

E.g. $x_1 = size / 200$, $x_2 = number / 5$

or 均值归一化：$x_n = \frac{x_n - \mu_n}{s_n}$ ($\mu_n$平均值，$s_n$标准差)

replace $x_i$ with $x_i - \mu_i$ to make features have approximately zero mean.

### 4.2. Learning Rate

purpose : to make sure gradient descent is working correctly

if it's working correctly, cost function will decrease after every loop. So we can graph No. of iterations and $min_\theta J(\theta)$ plot.

automatic convergence test : declare convergence if $J(\theta)$ decreases by less than $10^{-3}$ in one iteration.

by using these, you can choose a suitable $\alpha$ (learning rate).

thy ..., 0.001, ..., 0.003, ..., 0.01, ..., 0.03, ..., 0.1, ..., 0.3, ..., 1, ...

### 4.3. Features and Polynomial Regression

对特征进行组合来形成新的特征，能得到更好的model

如房价预测中。将房子的长与宽这两个特征进行组合，形成面积这一新的特征。

观察数据分布，定义多项式模型。

将数据分布图画出来，根据数据分布图的形状来大致判断模型应该是什么样。

### 4.4. Overfitting Problem

1. what is overfitting?

if we have too many features, the learned hypothesis may fit the training set very well ($J(\theta) \approx 0$), but fail to generalize to new examples (predict price on new examples).

in the same training set and different models

underfitting : high bias $\theta_0 + \theta_1 x$

overfitting : high variance $\theta_0 + \theta_1 x + \theta_2 x^2 + \theta_3 x^3 + \theta_4 x^4$

2. address overfitting

对于特征少的情况可以通过绘图来查看拟合情况。但是在特征多的情况下很难绘图。

options:

1. reduce numbers of features

   manually select which features to keep

   model selection algorithm

2. regularization

   keep all the features, but reduce magnitude / values of $\theta_j$

   works well when we have a lot of features, each of which contributes a bit to predict y

cost function : 前面的高次回归模型中，正是那些高次项导致了过拟合，所以如果我们能让高次项系数接近0，那么就能很好地解决过拟合。

E.g.  $h_\theta(x) = \theta_0 + \theta_1 x + \theta_2 x^2 + \theta_3 x^3 + \theta_4 x^4$

suppose we penalize and make $\theta_3$ and $\theta_4$ very small.

$min_\theta \frac{1}{2m} \sum_{i=1}^m [(h_\theta(x^{(i)} - y^{(i)})^2 + 1000 * \theta_3^2 + 1000 * \theta_4^2]$($\theta_3$, $\theta_4$ $\approx 0$)

regularization : small values for parameters $\theta_0,\theta_1,...,\theta_n$

​	simpler hypothesis

​	less prone to overfitting

因为特征项很多，不知道相关度谁高谁低，就应该对所有特征进行惩罚。

得到的cost function为：

$J(\theta) = \frac{1}{2m} [\sum_{i=1}^m (h_\theta(x^{(i)}) - y^{(i)})^2 + \lambda \sum_{j=1}^m \theta_j^2]$ (j = 1，说明不对$\theta_0$惩罚)

$\lambda$ is regularization parameter

