---
layout: post
title:  "机器学习：2015年机器学习小结"
date:   2016-1-30 7:1:32 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2016.1.30   

http://koforce.blogspot.com/2016/01/2015.html

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


去年年初就开始有花费时间在机器学习上边，不过一直断断续续，非常没有连贯性。年初看了Tom Mich 的CMU课程录像得一部分，然后把他神经网络的例子给跑起来了。不过就半途而废了，去看Andrew Ng的机器学习的开放课程。从头看到GLM，刚要看贝叶斯，就又没心思往下看了。然后就中断了半年。直到前两三个月，又开始看深度学习的知识，配置了Torch的简单深度学习网络。最近一个月就又没时间看机器学习，因为发现web编程是个很有意思的东西，开始倒弄python django框架。不过还是有必要稍微小结下今年的机器学习的进度。感觉机器学习的各个部分其实还相对独立，比如神经网络，决策时和GLM三者就基本上没有太大关系。这意味着其实机器学习可以阶段性的学习某个部分，最后也可以很好的拼接起来。

# 神经网络（深度学习）
神经网络在早期被退出后经历了第一次停滞期，主要是无法再在数值求解上很快收敛。然后某某大神提出了逆向反馈（传播？）算法，大大提高了求解速度。此后不知道为啥(梯度爆炸？)，又经历了一次停滞期，直到近年来计算机硬件的发展导致计算力大增，使多层神经网络也就是深度学习成为了可能。可见神经网络如今的如日中天并非一帆风顺。

暂且称首次提出的神经网络为原始神经网络，逆向算法的神经网络为传统神经网络（一般只有两层？），而最近的多层神经网络为深度学习。

## 原始神经网络
神经元？
## 传统神经网络
- 两层的神经网络被数学证明可以解决任何问题。
- 逆反馈。
- 训练算法：随机梯度下降。
- 过拟合

我把tom mich教材的c版本的传统神经网络（逆反馈，两层）改写成C++可运行版本，权当练习。[[github](https://github.com/lealzhan/SimpleNN_Tom_ML_Book)]

## 深度学习
深度学习近一两年突飞猛进，可称作是计算机界的最大突破。深度学习最令人瞩目的便是在图像识别上的极高准确率，现在可以说随随便便就超过了人眼识别率。。。业界也是在LeNet, alexNet的基础上衍生出诸多深度学习网络的变种，如google的googlenet。。

## Torch
学习的库是Torch。优点：google，facebook都在用；是个机器学习库，而不单单是深度学习库。缺点：lua脚本；社区远不如caffe不活跃 。
学习Torch的话，基本上就是理解了Torch教程里LeNet。lua代码见 [[github](https://github.com/lealzhan/torch_practice/tree/master/0.%20torch_dl_60m)]。
学习之后觉得并不是非常好用。现在也是先放在那边。

## 几种神经网络
- LeNet
- AlexNet: Krizhevsky, Alex, Ilya Sutskever, and Geoffrey E. Hinton. "Imagenet classification with deep convolutional neural networks." Advances in neural information processing systems. 2012. [[PDF](http://www.nvidia.ru/content/tesla/pdf/machine-learning/imagenet-classification-with-deep-convolutional-nn.pdf)]
- GoogLeNet: Szegedy, Christian, et al. "Going deeper with convolutions." arXiv preprint arXiv:1409.4842 (2014).
- VGGNet

## Reference
- Mitchell, Tom M. 机器学习. 机械工业出版社, 2003. [[Course](http://www.cs.cmu.edu/~tom/10701_sp11/)]
- 邱锡鹏，神经网络与深度学习讲义，2015.12
- Geoffrey Hinton, Neural Networks for Machine Learning [[Coursera](https://www.coursera.org/course/neuralnets)]
- Deep Learning, Nature Review [[LINK](http://download.csdn.net/download/happytofly/8758755)] [[Chinese](http://www.csdn.net/article/2015-06-01/2824811)]
- Ilya Sutskever, A Brief Overview of Deep Learning [[LINK](http://yyue.blogspot.ru/2015/01/a-brief-overview-of-deep-learning.html)]
- 深度学习三十年创新路 [[LINK](http://36kr.com/p/533832.html)]
- 卷积神经网络及目标检测调研, cnndoer@gmail.com
- Jiaxin Shi, Deep Convolutional Nets.ppt, 2015.3


