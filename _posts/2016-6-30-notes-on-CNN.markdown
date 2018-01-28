---
layout: post
title:  "Notes on CNN"
date:   2016-6-30 7:1:32 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2016.6.30   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

http://koforce.blogspot.com/2016/03/note.html?zx=be9347e32cf74d0d

# 2016.3.30 Convolutional Neural Networks for Sentence Classification

本文是CNN用于自然语言处理的开山之作，并已成为标准。

- implemented in TensorFlow (python, kindof slow)
- 目的：给出一个语句，判断其情感（高兴，不高兴等等）
- 样本：几千个语句，已经标记了情感
- 学习模型：CNN

![](https://4.bp.blogspot.com/-r_ySSwmKba4/VvtuuuaMWUI/AAAAAAAAHZQ/WE8WrfsU7BQpDrAM71pwRisShmHhDqfyg/s1600/Convolutional%2BNeural%2BNetworks%2Bfor%2BSentence%2BClassification%2B.tiff)


## reference
想入门CNN用于自然语言的话，上述都可以忽略，好好看以下两篇博客就可以。

- Understanding Convolutional Neural Networks for NLP [[BLOG](http://www.wildml.com/2015/11/understanding-convolutional-neural-networks-for-nlp/)]
- Implementing a CNN for Text Classification in TensorFlow [[BLOG](http://www.wildml.com/2015/12/implementing-a-cnn-for-text-classification-in-tensorflow/)]


# 2016.4.7
## [转载]激活函数

Activation function　翻译成激活函数，不要误解是指这个函数去激活什么，而是指如何把“激活的神经元的特征”通过函数把特征保留并映射出来，这是神经网络能解决非线性问题关键。

激活函数众所周知有tanh,sigmoid,ReLU等。    
- tanh　　　双切正切函数，取值范围[-1,1]
- sigmoid　 采用S形函数，取值范围[0,1]
- ReLU 简单而粗暴，大于0的留下，否则一律为0。

因为神经网络的数学基础是**处处可微**的，所以选取的激活函数要能保证数据输入与输出也是可微的。

神经网络中，运算特征是不断进行循环计算，所以在每代循环过程中，每个神经元的值也是在不断变化的。

这就导致了tanh特征相差明显时的效果会很好，在循环过程中会不断扩大特征效果显示出来。
但有时候，特征相差比较复杂或是相差不是特别大时，需要更细微的分类判断的时候，sigmoid效果就好了。

所以sigmoid相比用得更多，但近年发现数据有一个很有意思的特征。

也就是稀疏性，数据有很多的冗余，而近似程度的最大保留数据特征，可以用大多数元素为0的稀疏矩阵来实现。

而Relu，它就是取的max(0,x)，因为神经网络是不断反复计算，实际上变成了它在尝试不断试探如何用一个大多数为0的矩阵来尝试表达数据特征，结果因为稀疏特性的存在，反而这种方法变得运算得又快效果又好了。

所以，据说，目前大多在用max(0,x)来代替sigmod函数了。

作者：三符
链接：https://www.zhihu.com/question/22334626/answer/53203202
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

ReLu(Rectified Linear Units)激活函数 [[link](http://www.mamicode.com/info-detail-873243.html)]

# n-grams
？
NLP涉及的问题
[LINK] 看不懂
Compared to something like n-grams, CNNs are also efficient in terms of representation. With a large vocabulary, computing anything more than 3-grams can quickly become expensive. Even Google doesn’t provide anything beyond 5-grams. Convolutional Filters learn good representations automatically, without needing to represent the whole vocabulary. It’s completely reasonable to have filters of size larger than 5. I like to think that many of the learned filters in the first layer are capturing features quite similar (but not limited) to n-grams, but represent them in a more compact way.

# 卷积网络基本概念
[[LINK](http://lan2720.github.io/2015/08/14/CNN%E5%AD%A6%E4%B9%A0%E4%B9%8B%E8%BF%9B%E9%98%B6/)]
[[LINK](http://cs231n.github.io/convolutional-networks/)]

## Narrow vs. Wide convolution


## Stride Size

![](http://www.wildml.com/wp-content/uploads/2015/11/Screen-Shot-2015-11-05-at-10.18.08-AM.png)
 
## Pooling Layers
统一输出向量维度，提取最主要信息，丢失信息位置。

![](http://www.wildml.com/wp-content/uploads/2015/11/Screen-Shot-2015-11-05-at-2.18.38-PM.png)

具体的：

在NLP中：
每个filter完成后，会生一个feature map。不同size的filter会生成不同size的feature map。因此，对于不同size的feature map，使用max pooling，就可以得到统一的一维向量。简单说，pooling 保证了sentence matrix 在经过不同size的filter处理后得到的univariate vector维度均为1。

这样的意义主要有两点：
（1）filter和univarite vector 一一对应的。univarite vector就是由filter生成的。最终生成的univarite vector的数量 和 使用的filter的数目是一样的，与sentence matrix的维度无关。  

按照[[LINK](http://lan2720.github.io/2015/08/14/CNN%E5%AD%A6%E4%B9%A0%E4%B9%8B%E8%BF%9B%E9%98%B6/)] 的说法：
This allows you to use variable size sentences, and variable size filters, but always get the same output dimensions to feed into a classifier. 

classifier 在下图中指的是 softmax function。

（2）提取了最主要的信息，但是 丢失了信息的位置。
比如说一个 d*2 size 的filter对于not amazing 会产生很高的权重。对于That is not amazing 这个句子，产生的feature map为 0 0 1。经过max pooling后，得到的univariate vector为 1 。 max poiling这样的意义是说明这个句子含有 not amazing这个词（而不是amazing not），但是位置信息是不被保留的。 这样有点相似  bag of n-grams model。

![](http://3.bp.blogspot.com/-LLNVmF-8g4s/VwYpxyG_JHI/AAAAAAAAHZ0/Sm__qez9IOgwzSnDBObsr0g1uL5nXPOBQ/s1600/Screen-Shot-2015-11-06-at-12.05.40-PM.png)

在图像识别中，提取主要信息，丢失了图片特征的移动和旋转信息。比如，在你识别某个区域时，在图片旋转移动不大的范围后，经过含有max pooling的神经网络的识别结果差别不大。

-------------- 2016.4.8  -------------------

每个filter的最终作用就是确认这个sentence含有某个特征的程度。这个程度在使用max pooling之前是一个和filter和sentence维度相关的feature map，使用max pooling之后，表征某个特征的程度就是一个一维向量。

``` python
sentence：That is not amazing

filter0：not amazing
|
V
feature map: 0 0 1
| max pooling
V
univariate vector: 1

filter1：play
|
V
feature map: 0 0 0 0
| max pooling
V
univariate vector: 0
```

意义：That is not amazing这个句子，含有not amazing（或者近义词？）。但是并不清楚not amazing在句子的那个位置。

-------------- reference -----------------

Source: Zhang, Y., & Wallace, B. (2015). A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional Neural Networks for Sentence Classification.

思考： filter的size和数量是怎么确定的？

在图像识别中
filter的size一般选5*5，3*3？。
filter的数量是？比如下图INPUT到C1的filter为啥是6个？

![](https://camo.githubusercontent.com/6396443a7d29d398806dd54c6d7fdf27e1cf9e66/687474703a2f2f666173746d6c2e636f6d2f696d616765732f63696661722f6c656e6574352e706e67)

NLP的话
filter的size = d*h。d是由词语转换算法决定的，onehot，GloVe，Word2Vec都会产生不同的d。h一般选2到5？
filter的数量？比如上边NLP那个图中，为啥fiter是六个？




# 如何设计CNN？
按照[[LINK](http://lan2720.github.io/2015/08/14/CNN%E5%AD%A6%E4%B9%A0%E4%B9%8B%E8%BF%9B%E9%98%B6/)] 的说法，对于NLP的CNN主要以下几个hyperparameters：
Building a CNN architecture means that there are many hyperparameters to choose from, some of which I presented above: Input represenations (word2vec, GloVe, one-hot), number and sizes of convolution filters, pooling strategies (max, average), and activation functions (ReLU, tanh).

按照[[LINK](http://lan2720.github.io/2015/08/14/CNN%E5%AD%A6%E4%B9%A0%E4%B9%8B%E8%BF%9B%E9%98%B6/)] 的说法，文章*详细比较了不同CNN 结构（hyperparameters）的对于NLP的Sentence Classification的实际效果，有以下几点值得注意：
A few results that stand out are that max-pooling always beat average pooling, that the ideal filter sizes are important but task-dependent, and that regularization doesn’t seem to make a big different in the NLP tasks that were considered.

----------------------------------------------------   
* A Sensitivity Analysis of (and Practitioners’ Guide to) Convolutional Neural Networks for Sentence Classification




# 读 Spatial Pyramid Pooling in Deep Convolutional 若干思考

公司同事分享的文章。cnn用于物体识别领域的最新力作。

``` python
?
|
V
R-CNN 
|                         
V                         
SPP-Net      
|
V
fast R-CNN
|
V
faster R-CNN
```

## SPP-Net 较R-CNN有了100倍提速
提速的原因：
原始图片标记为INPUT （227*227）

``` python
R-CNN
INPUT --> 2000个不同尺寸的候选长方形框 --> CNN --> 特征向量（4096） --> SVM分类
                                                                                 /\
                                                    卷积层 --> 特征图 -->  pooling --> 全连层
整个CNN跑了2000遍

SPP-Net
INPUT --> CNN --> 特征向量 --> SVM分类
                    /\
卷积层 --> 特征图 -->  2000个不同尺寸的候选长方形框 --> pooling --> 全连层

pooling --> 全连层 跑了2000遍
```
## 第1点可以实现的原因主要是用SPP解决了pooling只能接受固定尺寸数据的约束。

>RCNN 解决的是，“为什么不用CNN做classification呢？”（但是这个方法相当于过一遍network出bounding box，再过另一个出label，原文写的很不“elegant”
>
>Fast-RCNN 解决的是，“为什么不一起输出bounding box和label呢？”（但是这个时候用selective search generate regional proposal的时间实在太长了
>Faster-RCNN 解决的是，“为什么还要用selective search呢？”
>于是就达到了real-time。开山之作确实是开山之作，但是也是顺应了“Deep learning 搞一切vision”这一潮流吧。
>作者：齐浩之
>链接：https://www.zhihu.com/question/35887527/answer/72884459


# CNN应用的领域

自然语言处理 [[BLOG](http://www.wildml.com/2015/11/understanding-convolutional-neural-networks-for-nlp/)]
-句子情感分类

物体识别 [[zhihu](https://www.zhihu.com/question/35887527)]
- R-CNN ， SPP-Net ，fast R-CNN



