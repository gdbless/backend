零基础入门资源：

@hanbingtao
<a href="https://www.zybuluo.com/hanbingtao/note/433855">零基础入门深度学习：</a>

-------

语法解析树

<div align=center>
<img src="https://img-blog.csdn.net/20180905135454417?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N4bHN4bDExOQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70">
</div>

-------
# 神经网络一般过程：
来源：<a href="https://victorzhou.com/blog/keras-neural-network-tutorial/#2-preparing-the-data">Victor Zhou</a>

1. 准备数据集合

    - 数据前处理

2. 分割数据集
    - 准备训练数据
    - 准备测试数据

3. 构建模型
    - 神经网络构建

4. 编译模型

    - 优化器
    - 损失函数
    - 准确率指标

5. 训练模型
    - training data（图片和标签）
    - Epochs numbers
    - batch size

6. 测试模型

7. 使用模型
    - 构建模型
    - 载入训练模型

8. 预测结果

--------

# 神经网络需要多少隐藏层、每层需要多少神经元？

来源：<a href="https://zhuanlan.zhihu.com/p/47519999">论智</a>

该使用多少层隐藏层？使用隐藏层的目的是什么？增加隐藏层/神经元的数目总能给出更好的结果吗？人工神经网络（ANN）初学者常常提出这些问题。如果需要解决的问题很复杂，这些问题的答案可能也会比较复杂。希望读完这篇文章后，你至少可以知道如何回答这些问题。

<div align=center>
<img src="https://pic1.zhimg.com/v2-0dcdbc8f42e787634dcf812a5e9df0d8_r.jpg">
</div>

## 介绍
在计算机科学中，借鉴了生物神经网络的ANN用一组网络层表示。这些网络层可以分为三类：输入层、隐藏层、输出层。

输入层和输出层的层数、大小是最容易确定的。每个网络都有一个输入层，一个输出层。输入层的神经元数目等于将要处理的数据的变量数。输出层的神经元数目等于每个输入对应的输出数。不过，确定隐藏层的层数和大小却是一项挑战。

下面是在分类问题中确定隐藏层的层数，以及每个隐藏层的神经元数目的一些原则：

- 在数据上画出分隔分类的期望边界。
- 将期望边界表示为一组线段。
- 线段数等于第一个隐藏层的隐藏层神经元数。
- 将其中部分线段连接起来（每次选择哪些线段连接取决于设计者），并增加一个新隐藏层。也就是说，每连接一些线段，就新增一个隐藏层。
- 每次连接的连接数等于新增隐藏层的神经元数目。
下面我们将举例说明这一确定隐藏层层数、大小的简单方法。


## 例一

让我们先来看一个简单的分类问题。每个样本有两个输入和一个表示分类标签的输出，和XOR问题很像。


<div align=center>
<img src="https://pic3.zhimg.com/80/v2-0294b1c7bd73358e569ac04c6d4fbbb2_720w.webp">
</div>

首先需要回答的问题，是否需要隐藏层。关于这个问题，有一条一般规则：

>在神经网络中，当且仅当数据必须以非线性的方式分割时，才需要隐藏层。

回到我们的例子。看起来一条直线搞不定，因此，我们需要使用隐藏层。在这样的情形下，也许我们仍然可以不用隐藏层，但会影响到分类精确度。所以，最好使用隐藏层。

已知需要隐藏层，那么接下来就需要回答两个重要问题：

1. 需要多少层？
2. 每层需要多少神经元？

按照我们之前提到的流程，首先需要画出分割的边界。如下图所示，可能的边界不止一种。我们在之后的讨论中将以下图右部的方案为例。


<div align=center>
<img src="https://pic4.zhimg.com/80/v2-b228da8bf64044380fc9a592d8f07b1f_720w.webp">
</div>

根据之前的原则，接下来是使用一组线段表示这一边界。

使用一组线段表示边界的想法来自于神经网络的基础构件——单层感知器。单层感知器是一个线性分类器，根据下式创建分界线：

$$y=w_1x_1+w_2x_2+\dots+w_ix_i+b$$

其中 $x_i$ 为第 $i$ 项输入，$w_i$ 是权重，$b$ 是偏置，$y$ 是输出。因为每增加一个隐藏单元都会增加权重数，所以一般建议使用能够完成任务的最少数量的隐藏单元。隐藏神经元使用量超出需要会增加复杂度。

回到我们的例子上来，人工神经网络基于多个感知器构建，这就相当于网络由多条直线组成。

因此我们使用一组线段替换边界，以分界曲线变向处作为线段的起点，在这一点上放置方向不同的两条线段。

如下图所示，我们只需要两条线段（分界曲线变向处以空心圆圈表示）。也就是两个单层感知器网络，每个感知器产生一条线段。

只需两条线段就可以表示边界，因此第一个隐藏层将有两个隐藏神经元。

到目前为止，我们有包含两个隐藏神经元的单隐藏层。每个隐藏神经元可以看成由一条线段表示的一个线性分类器。每个分类器（即隐藏神经元）都有一个输出，总共有两个输出。但我们将要创建的是基于单个输出表示分类标签的一个分类器，因此，两个隐藏神经元的输出将被合并为单个输出。换句话说，这两条线段将由另一个神经元连接起来，如下图所示。

<div align=center>
<img src="https://pic1.zhimg.com/80/v2-d2abe7cfce8f048f806350d0e8285190_720w.webp">
</div>


很幸运，我们并不需要额外添加一个包含单个神经元的隐藏层。输出层的神经元正好可以起到这个作用，合并之前提到的两个输出（连接两条线段），这样整个网络就只有一个输出。

整个网络架构如下图所示：

<div align=center>
<img src="https://pic2.zhimg.com/80/v2-13bb78dbb6d0aca60a06ce9ebb32da8d_720w.webp">
</div>


## 例二

我们再来看一个分类问题的例子。和上面一个例子相似，这个例子也有两个分类，每个样本对应两个输入和一个输出。区别在于边界比之前的更复杂。

<div align=center>
<img src="https://pic1.zhimg.com/80/v2-36181cf3c68787d69cb5b71f79dd6100_720w.webp">
</div>

遵照之前的原则，第一步是画出边界（如下图左半部分所示），接着是将边界分成一组线段，我们将使用 **ANN** 的感知器建模每条线段。在画出线段之前，首先标出边界的变向处（下图右半部分中的空心圆圈）。

<div align=center>
<img src="https://pic4.zhimg.com/80/v2-5a697fbb0eb03b980f89de0d6d7220db_720w.webp">
</div>


问题在于需要几条线段？顶部和底部的变向处各需要两条线段，这样总共是4条线段。而当中的变向处可以和上下两个变向处共用线段。所以我们需要4条线段，如下图所示。

<div align=center>
<img src="https://pic2.zhimg.com/80/v2-8c1d778227debd70a201a389e1579235_720w.webp">
</div>

这意味着第一个隐藏层将包含4个神经元。换句话说，由单层感知器构成的4个分类器，每个分类器各生成一个输出，共计4个输出。接下来需要将这些分类器连接起来，使得整个网络生成单个输出。换句话说，通过另外的隐藏层将这些线段连接起来，以得到单条曲线。

网络的具体布局取决于模型设计者。一种可能的网络架构是创建包含两个隐藏神经元的第二隐藏层。其中第一个隐藏神经元连接前两条线段，最后一个隐藏神经元连接后两条线段，如下

<div align=center>
<img src="https://pic4.zhimg.com/80/v2-bfcd09545c2808d5dce069bd9e71e1eb_720w.webp">
</div>

到目前为止，我们有两条曲线，也就是两个输出。接下来我们连接这两条曲线，以得到整个网络的单个输出。在这一情形下，输出层的神经元可以完成最终的连接，而无需增加一个新的隐藏层。最后我们得到了如下曲线：

<div align=center>
<img src="https://pic2.zhimg.com/80/v2-dd2bcb85cba9b7d260a016983914999d_720w.webp">
</div>

这就完成了网络的设计，整个网络架构如下图所示：



--------
# 三次简化一张图：一招理解LSTM/GRU门控机制
来源：张皓 <a href="https://zhuanlan.zhihu.com/p/28297161?utm_id=0">https://zhuanlan.zhihu.com/p/28297161?utm_id=0</a>

RNN在处理时序数据时十分成功。但是，对RNN及其变种LSTM和GRU结构的理解仍然是一个困难的任务。本文介绍一种理解LSTM和GRU的简单通用的方法。通过对LSTM和GRU数学形式化的三次简化，最后将数据流形式画成一张图，可以简洁直观地对其中的原理进行理解与分析。此外，本文介绍的三次简化一张图的分析方法具有普适性，可广泛用于其他门控网络的分析。

## 1. RNN、梯度爆炸与梯度消失
### 1.1 RNN

近些年，深度学习模型在处理有非常复杂内部结构的数据时十分有效。例如，图像数据的像素之间的2维空间关系非常重要，CNN（convolution neural networks，卷积神经网络）处理这种空间关系十分有效。而时序数据（sequential data）的变长输入序列之间时序关系非常重要，RNN（recur**rent** neural networks，循环神经网络，注意和recur**sive** neural networks，递归神经网络的区别）处理这种时序关系十分有效。

我们使用下标 $t$ 表示输入时序序列的不同位置，用 $\boldsymbol{h}_t$ 表示在时刻 $t$ 的系统隐层状态向量，用 $\boldsymbol{x}_t$ 表示时刻 $t$ 的输入。$t$ 时刻的隐层状态向量 $\boldsymbol{h}_t$ 依赖于当前词 $\boldsymbol{x}_t$ 和前一时刻的隐层状态向量 $\boldsymbol{h}_{t-1}$ ：

$$\boldsymbol{h}_t:=\boldsymbol{f}(\boldsymbol{x}_t,\boldsymbol{h}_{t-1}),$$

其中 $\boldsymbol{f}$ 是一个非线性映射函数。一种通常的做法是计算 $\boldsymbol{x}_t$ 和 $\boldsymbol{h}_{t-1}$ 的线性变换后经过一个非线性激活函数，例如

$$\boldsymbol{h}_t := tanh(\boldsymbol{W}_{xh}\boldsymbol{x}_t + \boldsymbol{W}_{hh}\boldsymbol{h}_{t-1}),$$

其中 $\boldsymbol{W}_{xh}$ 和 $\boldsymbol{W}_{hh}$是可学习的参数矩阵，激活函数tanh独立地应用到其输入的每个元素。

为了对 RNN 的计算过程做一个可视化，我们可以画出下图：

<div align=center>
<img src="https://pic3.zhimg.com/80/v2-d1bb081b599d160e3cde8a0d3cdd062e_720w.webp">
</div>

图中左边是输入 $\boldsymbol{x}_t$ 和 $\boldsymbol{h}_{t-1}$、右边是输出 $\boldsymbol{h}_t$ 。计算从左向右进行，整个运算包括三步：输入 $\boldsymbol{x}_t$ 和 $\boldsymbol{h}_t$ 分别乘以 $\boldsymbol{W}_{xh}$ 和 $\boldsymbol{W}_{hh}$ 、相加、经过 tanh 非线性变换。

我们可以认为 $h_t$ 储存了网络中的记忆（memory），RNN 学习的目标是使得 $h_t$ 记录了在 $t$ 时刻之前（含）的输入信息 $\boldsymbol{x}_1, \boldsymbol{x}_2, \boldsymbol{\dots,}\ \boldsymbol{x}_t$ 。在新词 $\boldsymbol{x}_t$ 输入到网络之后，之前的隐状态向量 $\boldsymbol{h}_{t-1}$ 就转换为和当前输入 $\boldsymbol{x}_t$ 有关的 $\boldsymbol{h}_t$ 。

## 1.2 梯度爆炸与梯度消失

虽然理论上RNN可以捕获长距离依赖，但实际应用中，RNN将会面临两个挑战：梯度爆炸（gradient explosion）和梯度消失（vanishing gradient）。

我们考虑一种简单情况，即激活函数是恒等（identity）变换，此时

$$\boldsymbol{h}_t:=\boldsymbol{W}_{xh}x_t + \boldsymbol{W}_{hh}\boldsymbol{h}_t-1$$

在进行误差反向传播（error backpropagation）时，当我们已知损失函数 $\ell$ 对  时刻隐状态向量 $\boldsymbol{h}_t$ 的偏导数 $\frac{\partial{\ell}}{\partial{\boldsymbol{h}_0}}$ 时，利用链式法则，我们计算损失函数 $\ell$ 对 $t$ 时刻隐状态向量 $\boldsymbol{h}_0$ 的偏导数

$$\frac{\partial{\ell}}{\partial{\boldsymbol{h}_0}}=(\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}})^{\top}$$

我们可以利用 RNN 的依赖关系，沿时间维度展开，来计算 $\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}}$

$$\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}}=\prod_{i=1}^{t}{\frac{\partial{\boldsymbol{h}_i}}{\partial{\boldsymbol{h}_{i-1}}}}=\prod_{i=1}^{t}{\boldsymbol{W}_{hh}}=\boldsymbol{W}_{hh}^{t}.$$

其中，$\boldsymbol{{W}}_{hh}^{t}$ 表示矩阵  ​的​ $\boldsymbol{{W}}_{hh}$ 次幂，注意不是矩阵的转置。从上式可以看出，在误差反向传播时我们需要反复乘以参数矩阵 $\boldsymbol{{W}}_{hh}$ 。

由于矩阵 $\boldsymbol{{W}}_{hh}$ 通常是由随机初始化训练得到的，所以 $\boldsymbol{{W}}_{hh}$ 的特征向量通常时独立的，因此我们可以对矩阵 $\boldsymbol{{W}}_{hh}$​ 进行对角化：

$$\boldsymbol{{W}}_{hh}=\boldsymbol{S\Lambda S^{-1}}$$

其中

$$\boldsymbol{\Lambda}:=diag(\lambda_1,\lambda_2,\dots,\lambda_n),\ S:=[\boldsymbol{s}_1,\boldsymbol{s}_2\dots\boldsymbol{s}_n],\ S^{-1}:=
\begin{bmatrix}
v_1^{\top}\\
v_2^{\top}\\
\dots\\
v_n^T
\end{bmatrix}.$$

因此，

$$\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}}=\boldsymbol{W}_{hh}^{t}=\boldsymbol{S\Lambda S^{-1}}=\boldsymbol{S\Lambda ^{t} S^{-1}}=\sum_{i=1}^{n}{\lambda ^{t}\boldsymbol{s}_i\boldsymbol{v}_i^{\top}}$$

那么我们最后要计算的目标

$$\frac{\partial{\ell}}{\partial{\boldsymbol{h}_0}}
=(\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}})^{\top}=(\sum_{i=1}^{n}{\lambda_i^t\boldsymbol{s}_i\boldsymbol{v}_i^{\top}})\frac{\partial{\ell}}{\partial{\boldsymbol{h}_t}}=\sum_{i=1}^{r}{\lambda_i^t\boldsymbol{s}_i\boldsymbol{v}_i^{\top}}\frac{\partial{\ell}}{\partial{\boldsymbol{h}_t}}.$$

当 $t$ 很大时，该偏导数取决于矩阵 $\boldsymbol{W}_{hh}$ 对应的对角矩阵的最大值 $\lambda_1$ 是大于1还是小于1，要么结果太大，要么结果太小：

<div align=center>
<img src="https://pic1.zhimg.com/80/v2-db6bc7b10f19b540c3f1be154a22ec68_720w.webp">
</div>

(1). 梯度爆炸。当$\lambda_1\gt1,\ \lim_{t\rightarrow\infty}{\lambda_1^t}=\infty$，那么

$$\frac{\partial{\ell}}{\partial{\boldsymbol{h}_0}}
=\sum_{i=1}^{r}{\lambda_i^t\boldsymbol{s}_i\boldsymbol{v}_i^{\top}}\frac{\partial{\ell}}{\partial{\boldsymbol{h}_t}}\approx\infty\cdot\boldsymbol{v}_1\boldsymbol{s}_1\frac{\partial{\ell}}{\partial{\boldsymbol{h}_t}}=\infty.$$

此时偏导数 $\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}}$ 将会变得非常大，实际在训练时将会遇到 NaN 错误，会影响训练的收敛，甚至导致网络不收敛。这好比要把本国的产品卖到别的国家，结果被加了层层关税，等到了别国市场的时候，价格已经变得非常高，老百姓根本买不起。在 RNN 中，梯度（偏导数）就是价格，随着向前推移，梯度越來越大。这种现象称为梯度爆炸。

梯度爆炸相对比较好处理，可以用梯度裁剪（gradient clipping）来解决：

<div align=center>
<img src="https://pic4.zhimg.com/80/v2-2efd0ea742d55466256a581bc490927b_720w.webp">
</div>

这好比是不管前面的关税怎么加，设置一个最高市场价格，通过这个最高市场价格保证老百姓是买的起的。在 RNN 中，不管梯度回传的时候大到什么程度，设置一个梯度的阈值，梯度最多是这么大。

(2). **梯度消失**。当 $\lambda_1\lt1,\ \lim_{t\rightarrow\infty}{\lambda_1^t}=0$，那么

$$\frac{\partial{\ell}}{\partial{\boldsymbol{h}_0}}=\sum_{i=1}^{r}{\lambda_i^t\boldsymbol{v}_i\boldsymbol{s}_i^{\top} \frac{\partial{\ell}}{\partial{\boldsymbol{h}_t}}} \approx 0\cdot \boldsymbol{v}_1\boldsymbol{s}_1\frac{\partial{\ell}}{\partial{\boldsymbol{h}_t}}=0.$$

此时偏导数 $\frac{\partial{\boldsymbol{h}_t}}{\partial{\boldsymbol{h}_0}}$ 将会变得十分接近0，从而在梯度更新前后没有什么区别，这会使得网络捕获长距离依赖（long-term dependency）的能力下降。这好比打仗的时候往前线送粮食，送粮食的队伍自己也得吃粮食。当补给点离前线太远时，还没等送到，粮食在半路上就已经被吃完了。在 RNN 中，梯度（偏导数）就是粮食，随着向前推移，梯度逐渐被消耗殆尽。这种现象称为梯度消失。

梯度消失现象解决起来困难很多，如何缓解梯度消失是 RNN 及几乎其他所有深度学习方法研究的关键所在。 LSTM 和GRU通过门（gate）机制控制RNN中的信息流动，用来缓解梯度消失问题。其核心思想是有选择性的处理输入。比如我们在看到一个商品的评论时

>Amazing! This box of cereal gave me a perfectly balanced breakfast, as all things should be. In only ate half of it but will definitely be buying again!

我们会重点关注其中的一些词，对它们进行处理

>**Amazing!** This box of cereal gave me a **perfectly balanced breakfast**, as all things should be. In only ate half of it but will **definitely be buying again**!

LSTM 和 GRU 的关键是会选择性地忽略其中一些词，不让其参与到隐层状态向量的更新中，最后只保留相关的信息进行预测。

## 2. LSTM
### 2.1 LSTM 的数学形式

LSTM（Long Short-Term Memory）由 Hochreiter 和 Schmidhuber 提出，其数学上的形式化表示如下:

$$
\begin{aligned}
&\boldsymbol{i}_t:=sigm(\boldsymbol{W}_{xi}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hi}\boldsymbol{h}_{t-1})\\

&\boldsymbol{f}_t:=sigm(\boldsymbol{W}_{xf}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hf}\boldsymbol{h}_{t-1})\\

&\boldsymbol{o}_t:=sigm(\boldsymbol{W}_{xo}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{ho}\boldsymbol{h}_{t-1})\\

&\boldsymbol{\widetilde{c}}_t^:=tanh(\boldsymbol{W}_{xi}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hi}\boldsymbol{h}_{t-1})\\

&\boldsymbol{c}_t:=\boldsymbol{f}_t \odot \boldsymbol{c}_{t-1} + \boldsymbol{i}_t\odot \widetilde{\boldsymbol{c}}_t\\

&\boldsymbol{h}_t=\boldsymbol{o}_t\odot tanh(\boldsymbol{c}_t)

\end{aligned}
$$

其中 $\odot$ 代表逐元素相乘，sigm 代表 sigmoid 函数

$$sigma(z):=\frac{1}{1+exp(-z)}.$$

和RNN相比，LSTM 多了一个隐状态变量 $\boldsymbol{c}_t$ ，称为细胞状态（cell state），用来记录信息。

这个公式看起来似乎十分复杂，为了更好的理解 LSTM 的机制，许多人用图来描述 LSTM 的计算过程。比如下面这张图：

<div align=center>
<img src="https://pic1.zhimg.com/80/v2-6a97bbf66211569a33c38c8e255ed6c8_720w.webp">
</div>

似乎看完之后，对 LSTM 的理解仍然是一头雾水？这是因为这些图想把 LSTM 的所有细节一次性都展示出来，但是突然暴露这么多的细节会使你眼花缭乱，从而无处下手。

### 2.2 简化一张图

因此，本文提出的方法旨在简化门控机制中不重要的部分，从而更关注在LSTM的核心思想。整个过程是**三次简化一张图**，具体流程如下：

**(1). 第一次简化：忽略门控单元 $\boldsymbol{i}_t$ 、$\boldsymbol{f}_t$ 、$\boldsymbol{o}_t$ 的来源**。3个门控单元的计算方法完全相同，都是由输入经过线性映射得到的，区别只是计算的参数不同：

$$
\begin{aligned}
&\boldsymbol{i}_t:=sigm(\boldsymbol{W}_{xi}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hi}\boldsymbol{h}_{t-1})\\

&\boldsymbol{f}_t:=sigm(\boldsymbol{W}_{xf}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hf}\boldsymbol{h}_{t-1})\\

&\boldsymbol{o}_t:=sigm(\boldsymbol{W}_{xo}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{ho}\boldsymbol{h}_{t-1})

\end{aligned}
$$

**(2). 第二次简化：考虑一维门控单元 $i_t$ 、$f_t$ 、$o_t$**。LSTM 中对各维是独立进行门控的，所以为了表示和理解方便，我们只需要考虑一维情况，在理解 LSTM 原理之后，将一维推广到多维是很直接的。经过这两次简化，LSTM 的数学形式只有下面三行
$$
\begin{aligned}
&\boldsymbol{\widetilde{c}}_t^:=tanh(\boldsymbol{W}_{xi}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hi}\boldsymbol{h}_{t-1})\\

&\boldsymbol{c}_t:=\boldsymbol{f}_t \odot \boldsymbol{c}_{t-1} + \boldsymbol{i}_t\odot \widetilde{\boldsymbol{c}}_t\\

&\boldsymbol{h}_t=\boldsymbol{o}_t\odot tanh(\boldsymbol{c}_t)

\end{aligned}
$$

由于门控单元变成了一维，所以向量和向量的逐元素相乘符号 $\odot$ 变成了数和向量相乘 $\cdot$ 。

**(3). 第三次简化：各门控单元二值输出**。门控单元 $i_t$ 、$f_t$ 、$o_t$ 的由于经过了sigmoid 激活函数，输出是范围是[0, 1]。激活函数使用sigmoid 的目的是为了近似 0/1 阶跃函数，这样 sigmoid 实数值输出单调可微，可以基于误差反向传播进行更新。

<div align=center>
<img src="https://pic4.zhimg.com/80/v2-49a9018881b992fb140b99285069698b_720w.webp">
</div>

既然 sigmoid 激活函数是为了近似 0/1 阶跃函数，那么，在进行 LSTM 理解分析的时候，为了理解方便，我们认为各门控单元 {0, 1} 二值输出，即门控单元扮演了电路中开关的角色，用于控制信息的通断。

**(4). 一张图**。将三次简化的结果用电路图表述出来，左边是输入，右边是输出。在 LSTM 中，有一点需要特别注意，LSTM 中的细胞状态 $\boldsymbol{c}_t$ 实质上起到了 RNN 中隐层单元 $\boldsymbol{h}_t$ 的作用，这点在其他文献资料中不常被提到，所以整个图的输入是 $\boldsymbol{x}_t$ 和 $\boldsymbol{c}_{t-1}$ ，而不是 $\boldsymbol{x}_t$ 和 $\boldsymbol{h}_{t+1}$。为了方便画图，我们需要将公式做最后的调整

$$
\begin{aligned}
\boldsymbol{h}_{t-1}&:=o_{t-1}\cdot tanh(\boldsymbol{c}_{t-1})\\

\widetilde{\boldsymbol{c}_t}&:=tanh(\boldsymbol{W}_{xc}\boldsymbol{x}_t\ +\ \boldsymbol{W}_{hc}\boldsymbol{h}_{t-1}),\\

\boldsymbol{c}_t&:=f_t\cdot\boldsymbol{c}_{t-1}+i_t\cdot \boldsymbol{\widetilde{c}_t}.

\end{aligned}
$$

最终结果如下：

<div align=center>
<img src="https://pic4.zhimg.com/80/v2-7f74a75ca3b252bac92f0e7a77446fff_720w.webp">
</div>

和 RNN 相同的是，网络接受两个输入，得到一个输出。其中使用了两个参数矩阵 $\boldsymbol{W}_{xc}$ 和 $\boldsymbol{W}_{hc}$ ，以及tanh激活函数。不同之处在于，LSTM 中通过 3 个门控单元 $i_t$ 、$f_t$ 、$o_t$ 来对的信息交互进行控制。当 $i=1$ （开关闭合）、$f=0$ （开关打开）、$o_t=1$ （开关闭合）时，LSTM退化为标准的 RNN。

### 2.3 LSTM 各单元作用分析

根据这张图，我们可以对LSTM中各单元作用进行分析：

- **输入门 $o_{t-1}$**：输出门的目的是从细胞状态 $\boldsymbol{c}_{t-1}$ 产生隐层单元 $\boldsymbol{h}_{t-1}$ 。并不是 $\boldsymbol{c}_{t-1}$ 中的全部信息都和隐层单元 $\boldsymbol{h}_{t-1}$ 有关，$\boldsymbol{c}_{t-1}$ 可能包含了很多对 $\boldsymbol{h}_{t-1}$ 无用的信息。因此，$o_t$  的作用就是判断 $\boldsymbol{c}_{t-1}$ 中哪些部分是对  有用的，哪些部分是无用的。

- **输入门 $i_{t}$**： 控制当前词 $\boldsymbol{x}_{t}$ 的信息融入细胞状态 $\boldsymbol{c}_{t}$ 。在理解一句话时，当前词 $\boldsymbol{x}_{t}$ 可能对整句话的意思很重要，也可能并不重要。输入门的目的就是判断当前词 $\boldsymbol{x}_{t}$ 对全局的重要性。当 $i_t$ 开关打开的时候，网络将不考虑当前输入 $\boldsymbol{x}_{t}$ 。

- **遗忘门 $f_{t}$**：$\boldsymbol{f}_{t}$ 控制上一时刻细胞状态 $\boldsymbol{c}_{t-1}$ 的信息融入细胞状态 $\boldsymbol{c}_{t}$ 。在理解一句话时，当前词 $\boldsymbol{x}_{t}$ 可能继续延续上文的意思继续描述，也可能从当前词 $\boldsymbol{x}_{t}$ 开始描述新的内容，与上文无关。和输入门 $i_t$ 相反，$f_t$ 不对当前词 $\boldsymbol{x}_{t}$ 的重要性作判断，而判断的是上一时刻的细胞状态 $\boldsymbol{c}_{t-1}$ 对计算当前细胞状态 $\boldsymbol{c}_{t}$ 的重要性。当 $f_t$ 开关打开的时候，网络将不考虑上一时刻的细胞状态 $\boldsymbol{c}_{t-1}$ 。

- **细胞状态 $\boldsymbol{c}_{t}$**： $\boldsymbol{c}_{t}$ 综合了当前词 $\boldsymbol{x}_{t}$ 和前一时刻细胞状态 $\boldsymbol{c}_{t-1}$ 的信息。这和ResNet中的残差逼近思想十分相似，通过从 $\boldsymbol{c}_{t-1}$ 到 $\boldsymbol{c}_{t}$ 的“短路连接”，梯度得已有效地反向传播。当 $f_t$ 处于闭合状态时，$\boldsymbol{c}_{t}$ 的梯度可以直接沿着最下面这条短路线传递到 $\boldsymbol{c}_{t-1}$ ，不受参数 $\boldsymbol{W}_{xh}$ 和 $\boldsymbol{W}_{hh}$ 的影响，这是 LSTM 能有效地缓解梯度消失现象的关键所在。

## GRU
### GRU 的数学形式

GRU是另一种十分主流的RNN衍生物。 RNN和LSTM都是在设计网络结构用于缓解梯度消失问题，只不过是网络结构有所不同。GRU在数学上的形式化表示如下：

$$
\begin{aligned}
\boldsymbol{z}_t&:=sigm(\boldsymbol{W}_{xz}\boldsymbol{x}_t+\boldsymbol{W}_{hz}\boldsymbol{h}_{t-1}),\\

\boldsymbol{r}_t&:=sigm(\boldsymbol{W}_{xr}\boldsymbol{x}_t+\boldsymbol{W}_{hr}\boldsymbol{h}_{t-1}),\\

\boldsymbol{\widetilde{\boldsymbol{h}}}_t&:=tanh(\boldsymbol{W}_{xh}\boldsymbol{x}_t+\boldsymbol{W}_{hh}\boldsymbol{h}_{t-1}),\\

\boldsymbol{h}_t&:=(1-\boldsymbol{z}_t)\odot\widetilde{\boldsymbol{h}}_t+\boldsymbol{z}_t\odot\boldsymbol{h}_{t-1}.

\end{aligned}
$$

### 3.2 三次简化一张图

为了理解 GRU 的设计思想，我们再一次运用三次简化一张图的方法来进行分析：

**(1). 第一次简化：忽略门控单元 $z_t$ 和 $r_t$ 的来源**。

**(2). 考虑一维门控单元 $z_t$ 和 $r_t$**。经过这两次简化，GRU 的数学形式是以下两行

$$
\begin{aligned}
\boldsymbol{\widetilde{\boldsymbol{h}}}_t&:=tanh(\boldsymbol{W}_{xh}\boldsymbol{x}_t+\boldsymbol{W}_{hh}\boldsymbol{h}_{t-1}),\\

\boldsymbol{h}_t&:=(1-\boldsymbol{z}_t)\odot\widetilde{\boldsymbol{h}}_t+\boldsymbol{z}_t\odot\boldsymbol{h}_{t-1}.

\end{aligned}
$$

**(3). 第三次简化：各门控单元二值输出**。这里和 LSTM 略有不同的地方在于，当 $z_t=1$ 时 $\boldsymbol{t}=\boldsymbol{t-1}$ ；而当 $z_t=0$ 时，$\boldsymbol{\boldsymbol{h}}_t=\boldsymbol{\widetilde{\boldsymbol{h}}}_t$  。因此， $z_t$ 扮演的角色是一个个单刀双掷开关。

**(4). 一张图**。将三次简化的结果用电路图表述出来，左边是输入，右边是输出。

<div align=center>
<img src="https://pic1.zhimg.com/80/v2-5faedb3b0e8e9785d8d25e5cd2aade00_720w.webp">
</div>

与 LSTM 相比，GRU 将输入门 $i_t$ 和遗忘门 $f_t$ 融合成单一的更新门 $z_t$ ，并且融合了细胞状态 $\boldsymbol{c}_t$ 和隐层单元 $\boldsymbol{h}_t$ 。当 $r_t=1$ （开关闭合）、 $z_t=0$ （开关连通上面）GRU 退化为标准的 RNN。

### 3.3 GRU 各单元作用分析

根据这张图, 我们可以对GRU的各单元作用进行分析:

- **重置门 $r_t$**： $r_t$ 用于控制前一时刻隐层单元 $\boldsymbol{h}_{t-1}$ 对当前词 $\boldsymbol{x}_t$ 的影响。如果 $\boldsymbol{h}_{t-1}$ 对 $\boldsymbol{x}_t$ 不重要，即从当前词 $\boldsymbol{x}_t$ 开始表述了新的意思，与上文无关。那么开关 $r_t$ 可以打开，使得 $\boldsymbol{h}_{t-1}$ 对 $\boldsymbol{x}_t$ 不产生影响。

- **更新门 $z_t$**： $z_t$ 用于决定是否忽略当前词 $\boldsymbol{x}_t$ 。类似于 LSTM 中的输入门 $i_t$ ，$z_t$ 可以判断当前词 $\boldsymbol{x}_t$ 对整体意思的表达是否重要。当 $z_t$ 开关接通下面的支路时，我们将忽略当前词 $\boldsymbol{x}_t$ ，同时构成了从 $\boldsymbol{h}_{t-1}$ 到 $\boldsymbol{h}_t$ 的短路连接，这使得梯度得已有效地反向传播。和 LSTM 相同，这种短路机制有效地缓解了梯度消失现象，这个机制于highway networks十分相似。

## 4. 小结

尽管 RNN、LSTM、和 GRU 的网络结构差别很大，但是他们的基本计算单元是一致的，都是对 $\boldsymbol{x}_{t}$ 和 $\boldsymbol{h}_{t}$ 做一个线性映射加 tanh 激活函数，见三个图的红色框部分。他们的区别在于如何设计额外的门控机制控制梯度信息传播用以缓解梯度消失现象。LSTM用了3个门、GRU 用了2个，那能不能再少呢？MGU（minimal gate unit）尝试对这个问题做出回答，它只有一个门控单元。最后留个小练习，参考 LSTM 和 GRU 的例子，你能不能用三次简化一张图的方法来分析一下 MGU 呢？