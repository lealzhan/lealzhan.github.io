---
layout: post
title:  "机器学习：李航 统计学习方法 笔记"
date:   2017-12-19 17:1:32 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - machine learning
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false
use_math: true
---

詹令   
lealzhan@126.com    
2017.12.19  


# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


------

待整理

统计学习方法

- 监督学习
- 非监督学习
- 半监督学习
- 强化学习


监督学习方法

- 生成方法 Generative Approach： 
$$P(Y|X) = \frac{P(X,Y)}{P(X)} $$

	- 朴素贝叶斯模型
	- 隐式马尔科夫模型
- 判别方法 Discrimitive Approach：

	- k近邻/knn
	- 线性分类模型
		- 感知机
	- 决策树
	- 对数线性分类模型
		- logistic regression
		- 最大熵模型 maximun entropy model
	- 支持向量机
	- 提升方法
	- 条件随机场





# 统计学习方法三要素

对于统计学习方法中的监督学习，包含有以下三个要素。对于非监督学习，强化学习也同样拥有这三要素。


>机器学习的定义：如果一个计算机程序针对某类任务T的用P衡量的性能根据经验E来自我完善。那么我们称这个计算机程序在从经验E中学习，针对某类任务T，它的性能用P来衡量。 
>-- Machine Learning Tom M. Mitchell

|李航|Tom Mitchell|
|:--:|:--:|
|数据集|经验E|
|模型|任务T|
|策略|性能P|
|算法||



## **模型**

**模型**：从输入到输出的映射

- 概率模型：条件概率分布
$$P(Y|X)$$
- 非概率模型：决策函数(decision function) $$Y=f(X)$$

模型的假设空间(hypothesis space)：所有可能的模型（输入到输出的映射）的集合

$$ \mathcal{F} =\lbrace \mathcal{f} | Y=f(X) \rbrace $$

这是$$\mathcal{F}$$通常是由一个参数向量$$\theta$$决定的函数簇：

$$ \mathcal{F} =\lbrace \mathcal{f} | Y_{\theta} = f_{\theta} (X), \theta \in R^n \rbrace $$


## **策略**

策略就是选择恰当的风险最小化函数(也就是损失函数关于联合概率分布的期望)的估计。

先介绍相关概念。

### **损失函数 loss function**
也称为代价函数 cost function

- 度量模型一次预测的好坏

#### 常见的损失函数 
**0-1损失函数**

$$ L(Y, P(Y|X)) =
\begin{cases}
1, & Y!=f(X) \\
0, & Y=f(X)
\end{cases}$$


**平方损失函数 quadratic loss function**

$$ L(Y, P(Y|X)) = (Y-f(X))^2 $$

**绝对损失函数**

$$ L(Y, P(Y|X)) = |Y-f(X)| $$

**对数损失函数/对数似然损失函数**
log loss function/log-likehood function

$$ L(Y, P(Y|X)) = -logP(Y|X) $$


### **风险函数 和 经验风险函数**：

#### **风险函数 Risk Funciton**

- 风险函数是损失函数$$L(y,f(x))$$关于联合分布$$P(X,Y)$$的期望
- 风险函数risk funciton也称为期望损失expected loss/损失函数的期望
- 度量模型平均意义下的好坏

$$ R_{exp}(f) = E_p[L(Y, f(X))] = \int_{xy} {L(y,f(x))P(x,y)dxdy } $$

在一般的学习过程中，
$$P(X|Y)$$
是未知的, 所以期望损失$$R_{exp}(f)$$是不可求的，一般用以下的 **经验风险empirical risk**来近似。

#### **经验风险函数 Empirical Risk**

- 度量模型**关于训练集**的平均损失
- 当样本数量N足够大，经验风险函数趋于风险函数，学习效果好。
 
$$ R_{emp}(f) = \frac{1}{N} \sum_{i=1}^N { L(y_i,f(x_i)) } $$

### **风险最小化**
#### **经验风险最小化 Empirical Risk Minimization - ERM**

$$\min_{f \in F} \frac{1}{N} \sum_{i=1}^N { L(y_i,f(x_i)) } $$

样本数量足够大，经验风险最小化学习效果好。样本数量不够时，出现过拟合 over fitting。

当模型是**条件概率分布**
$$P(Y|X)$$
，损失函数是**对数损失函数**，经验风险最小化等价于**极大似然估计**.

```c
推导：todo
```

#### **结构风险最小化 Structural Risk Minimization**

- (和经验风险最小化相比而言)，结构风险最小化可以防止样本数量小时，出现过拟合。
- 经验风险最小化的正则化 regulagization，添加一个惩罚项 $$\lambda J(f)$$
- 正则化可以找到经验风险和模型复杂度同时小的模型

$$ R_{srm}(f) = \frac{1}{N} \sum_{i=1}^N { L(y_i,f(x_i)) } + \lambda J(f) $$


常见的惩罚项 $$J(f)$$有

$$\lambda J(f) = \frac{\lambda}{2} {||w||}^2 $$ 

$$\|w\|$$表示$$L_2$$范数

$$\lambda J(f) = \frac{\lambda}{2} ||w||_1 $$ 

$$\|w\|_1$$表示$$L_1$$范数

当模型是**条件概率分布** 
$$P(Y|X)$$
, 损失函数是**对数损失函数**，模型复杂度是**模型的先验概率**时, 结构风险最小化 等价于 贝叶斯估计的**最大后验概率估计**(Max posterior probability estimation, MAP)

```c
推导：todo
```

### **策略小结**

所谓的策略，就是选择恰当的风险最小化函数(也就是损失函数关于联合概率分布的期望)的估计。

|策略| |特例|
|-------:|-------:|-------:|
|风险最小 | 理想的学习的目标|
|经验风险最小 | 联合概率未知，  数据量大才有效，易过拟合 | 特例 **极大似然估计**|
|结构风险最小 | 经验风险最小的**正则化**（添加模型复杂度惩罚项），不易过拟合| 特例 贝叶斯估计的**最大后验概率估计**|

## **算法**
目标函数的最优化。

- 全局最优
- 高效





**概率可以有两种方法来计算**

- 极大似然估计法
 - 先验概率
 - 后验概率

概率论与数理统计 盛骤 参数估计

样本的**似然函数**   
：固定样本观察值 $$ x_1,x_2,...,x_n $$, 在$$\theta$$取值的可能范围$$\Theta$$内挑选时似然函数$$ L(\theta) = L(x_1, x_2,...,x_n;\theta) = \prad_{i=1}^n {p(x_i;\theta)}, \theta \in \Theta $$达到最大值的$$\hat \theta$$，作为参数$$\theta$$的估计值，即使$$ L(x_1, x_2,...,x_n;\hat \theta) =  max_{theta \in \Theta}  L(x_1, x_2,...,x_n; \theta) $$


则称 $$\hat \theta$$为$$\theta$$的**最大似然估计值**

因为L(\theta)和ln(L(\theta))在同一theta出取得极值，因此theta的估计值也可从下式(对数似然方程)求得（更简单）：

$$ \frac{dli(L(\theta))}{d\theta} = 0 $$



- 贝叶斯估计
todo

 - 先验概率
 - 后验概率





先验概率：$$P(c_i)$$

后验概率： $$P(c_i|w)$$   
$$w$$ 特征向量
$$c_i$$ 分类


**算法4.1 朴素贝叶斯算法**

1. 基于 极大似然估计或贝叶斯估计 计算先验概率及条件概率

	

2. 对于给定的实例，计算
3. 确定实例x的类

	$$y=argmax_{ck} P(Y=c_k)\ P(x^{(j) = x^{(j)| Y = c_k)$$





# KNN K近邻法

三要素确定后，k近邻法就确定了。其实完成了对特征空间的划分。

**1. 距离度量**


**2. k值的选择**
应用中，k值一般取比较小的值。通常采用**交叉验证法**来选取最优的k值。


**3. 分类决策规则**
多数表决规则 等价于 经验风险最小化。

## k近邻法的实现：kdtree

todo




# 感知机 Perceptron


$$f(x) = sign(w*x+b)$$

$$ sign(x) = +1, x>=0; -1, x<0 $$

损失函数L
连续可微
梯度
更新方式
- w<-w+eta y_i x_i & b<-b+eta y_i
- 由损失函数的梯度可得到更新方式，不是很理解





----


# logistic regression 和 最大熵模型

## 二项logistic regression

### 模型

$$P(Y=1|x) = \frac{exp(w*x+b)}{1+exp{(w*x+b)}}$$

$$P(Y=0|x) = \frac{1}{1+exp{(w*x+b)}}$$

其中 $$x=(x^{(1)}, x^{(2)}, x^{(3)}...,x^{(n)})^T$$, $$w=(w^{(1)}, w^{(2)}, w^{(3)}...,w^{(n)})^T$$


也可写成

$$P(Y=1|x) = \frac{exp(w*x)}{1+exp{(w*x)}}$$

$$P(Y=0|x) = \frac{1}{1+exp{(w*x)}}$$

其中 $$x=(x^{(1)}, x^{(2)}, x^{(3)}...,x^{(n)},1)^T$$, $$w=(w^{(1)}, w^{(2)}, w^{(3)}...,w^{(n)},b)^T$$

二项logistic regression比较两个条件概率的大小，将实例x分到概率值较大的那一类。

### 策略：目标函数

对数极大似然估计

$$L(w) = \sum_{i=1}^N [y_i(w*x_i)-log(1+exp(w*x_i)]$$

### 学习：求解目标函数的极小值

用梯度下降或者拟牛顿法

## 多项logistic regression

- 用于多类分类

Y 的取值集合 $${1,2,...,K}$$

$$P(Y=k|x) = \frac{exp(w_k*x)}{1+\sum_{k=1}^{K-1}exp{(w_k*x)}}, k=1,2,...,K-1$$

$$P(Y=K|x) = \frac{1}{1+\sum_{k=1}^{K-1}exp{(w_k*x)}}$$

这里 $$x \in R^{n+1}, w_k \in R^{n+1}$$

``` python


```

## 最大熵模型

todo

# 决策树 Decision Tree

- 既可用于分类classify，也可用于回归 regression
- 不太需要对数据进行预处理
- 预测的复杂度是 logN， N是用于训练的数据的个数。
- 对结果的决策过程可以用一连串if-then进行解释(白盒模型)

决策树学习的三个步骤:特征选择, 决策树生成, 决策树修剪

## 1. 特征选择
如果特征数量很多，可以只选择对训练数据有分类能力的特征。 (降维)  
如果你用一个特征的进行分类的结果和随机分类的结果没有很大差别，则称这个特征**没有分类能力**。   
通常特征选择的准则是**信息增益**或**信息增益比**。   

### 信息增益
#### 熵 entropy

- 随机变量不确定性的度量
- 设随机变量X取有限个离散值的随机变量，其概率分布为 $$ P(X=x_i) = p_i, i=1,2,...,n $$, 则随机变量X的熵定义为 

$$ H(X)=\sum_{i=1}^n{p_i log{p_i}} $$ 

- 又由上一点可见，**熵**只依赖与X的分布p 而和X的取值无关，故 熵可记为

$$ H(X)=H(p)=\sum_{i=1}^n{p_i log{p_i}} $$ 

- 对于贝努力分布( P(X=1)=p, P(X=0)=1-p, 0<=p<=1 ), **熵**为

$$ H(p) = -plog_2p-(1-p)log_2{(1-p)} $$ 

![]()




#### 条件熵 conditionary entropy

$$ H(Y|X) = \sum_{i=1}^n P(X=x_i)H(Y|X=x_i) $$

$$ H(Y|X=x_i) = ?$$

- **经验**熵，**经验**条件熵： 当熵和条件熵中的**概率**由**数据估计**（特别是极大似然估计）得到时，所对应的熵与条件熵分别称为 经验熵 empirical entropy和经验条件熵 empirical conditional entropy。 

#### 信息增益

- **信息增益**表示 得知特征X的信息而使得**类Y的信息的不确定性**减少的程度。
- 特征A 对 训练数据集D 的信息增益 $$g(D,A)$$,定义为集合D的**经验熵H(D)**与特征A给定条件下D的**经验条件熵H(D\|A)**之差，即

$$ g(D,A) = H(D) - H(D|A) $$

- 使数据集D的**信息增益**$$g(D,A)$$越大的特征A的分类能力越强。   

#### 信息增益比

$$ g_R(D,A) = \frac{H(D) - H(D|A)}{H_A(D)} $$

$$ H_A(D)? 特征数据集D关于特征A的值的熵$$


#### 概念汇总？没弄懂这几种熵的具体计算
   
$$H(D)$$ 数据集D的**经验熵**

$$ H(D) = -\sum_{k=1}^K \frac{|C_k|}{|D|} log_2{\frac{C_k}{D}} $$

D最终被分成了K个类, 
$$ |C_k| $$
表示数据集D中属于第k个类的数量。


$$H_A(D)$$ 数据集D关于特征A的值的熵

$$ H_A(D) = -\sum_{i=1}^n \frac{|D_i|}{|D|} log_2{\frac{D_i}{D}} $$

n为特征A的取值个数。


$$H(D|A)$$ 
数据集D在给定特征A下的**经验条件熵**

$$ H(D|A) = \sum_{i=1}^n \frac{|D_i|}{|D|}H(D_i) = -\sum_{i=1}^n { \frac{|D_i|}{|D|} \sum_{k=1}^K \frac{|D_ik|}{|D|} log_2{\frac{D_ik}{D_i}} } $$


## 2.决策树生成

### ID3算法

- 从根节点开始，对节点的所有可能的特征的计算其信息增益，选择信息增益最大的特征作为节点的特征，由该特征的不同取值建立子节点；在对子节点递归的调用以上方法，构建决策树；知道所有特征的信息增益均衡小或没有特征可以选择为止。最后得到一颗决策树。
- ID3相当于用极大似然法进行概率模型的选择。？
- 具体的：





## 3.决策树修剪


## CART 算法
 
- scikit-learn uses an optimised version of the CART algorithm.

https://www.cnblogs.com/yonghao/p/5135386.html


http://scikit-learn.org/stable/modules/tree.html


# SVM

|x|训练数据集|模型|学习策略|学习算法|
|-------:|-------:|-------:|-------:|-------:|
|线性可分支持向量机 | 线性可分|分离超平面$$w^**x+b^*=0$$及决策函数$$f(x)=sign(w^**x+b^*)$$|硬间隔最大化|凸二次规划
|线性支持向量机|近似线性可分|分离超平面$$w^**x+b^*=0$$及决策函数$$f(x)=sign(w^**x+b^*)$$|软间隔最大化|凸二次规划|
|非线性支持向量机|线性不可分||软间隔最大化|| 



## 线性可分支持向量机 Linear Support Vector Machine in Linearly Seperately Case

**模型**




**策略**




**算法**





> 凸二次规划问题 Convex Quadratic Programming
>todo








# 预处理-数据降维-PCA 主成分分析

https://www.cnblogs.com/steed/p/7454329.html






# 提升方法 Boosting


**提升方法**： 从弱学习算法出发，反复学习，得到一些列弱分类器（又称为基本分类器），然后组合这些弱分类器，构成一个强分类器。


**两个问题**

- 在每一轮如何改变训练数据的权值或概率分布（针对不同的分布来学习弱分类器）
- 如何将弱分类器组合成一个强分类器

Adaboost

- 提高那些被前一轮弱分类器错误分类样本的权值
- 弱分类器的误差率和权重成正比


## Adaboost
有代表性的提升算法。基本分类器不定。


## 提升树 Boosting Tree

**提升树**: 以**决策树**为基本分类器的提升方法.

$$f_M(x) = \sum_{m=1}^M T(x;\theta_m) $$

$$T(x;\theta_m)  决策树, \theta_m 决策树参数， M表示树的颗数$$

决策树

- 二叉分类树：分类问题
- 二叉回归树：回归问题

## EM算法




# 似然

似然函数
极大似然估计

reference 周志华 机器学习 p148 149

https://www.zhihu.com/question/54082000

http://blog.csdn.net/fwing/article/details/4850068

https://www.cnblogs.com/zhsuiy/p/4822020.html


http://blog.csdn.net/sunlylorn/article/details/19610589

https://www.zhihu.com/question/26201440

http://blog.sciencenet.cn/blog-520608-703219.html

http://blog.csdn.net/guohecang/article/details/52313046

https://baike.baidu.com/item/%E4%BC%BC%E7%84%B6%E5%87%BD%E6%95%B0/6011241?fr=aladdin

L(θ|x)=f(x|θ)这个等式表示的是对于事件发生的两种角度的看法。其实等式两遍都是表示的这个事件发生的概率或者说可能性。再给定一个样本x后，我们去想这个样本出现的可能性到底是多大。统计学的观点始终是认为样本的出现是基于一个分布的。那么我们去假设这个分布为f，里面有参数theta。对于不同的theta，样本的分布不一样。f(x|θ)表示的就是在给定参数theta的情况下，x出现的可能性多大。L(θ|x)表示的是在给定样本x的时候，哪个参数theta使得x出现的可能性多大。所以其实这个等式要表示的核心意思都是在给一个theta和一个样本x的时候，整个事件发生的可能性多大。就写这么多吧！希望你能够明白。明天给学生讲课也顺便讲讲。哈哈！

作者：冯龙
链接：https://www.zhihu.com/question/54082000/answer/138115757
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

http://www.jianshu.com/p/1c6efdbce226




------------------------------------------------


Machine Learning - Andrew NG

- Part 0: Supervised Learning

- Part 1: Learning Theory

- Part 2: Unsupervised Learning

  - 例子
    - 基因数据
    - 图像划分
    - Cocktail party problem: 
    - ICA Algorithm

- Part 3: Reinforcement Learning

要经过**一系列的判断**才做出最终的决定。

  - 机器人控制: 直升机/机器狗/机器人/车辆
  - 


> 基本的机器学习算法就像木匠的锤子和锯子，牛逼的木匠可以用锤子和锯子做出美妙的作品，类似的，基本的机器学习算法也可以被巧妙地应用



# Supervised Learning

```c
Linear Regression   
|\  
| \   
|   locally weighted regression (非常流行)  
|  
Probabilittic interpretation of Linear Regression  
|  
|  
|  
logistic regression  
|\  
| \  
|  \  
|   perceptron  
|  
Newton's Method  
```

## linear regression

例子 房价预测

size， bedroom -> 房价

### **Model**

linear regression

$$ h(x) = \Theta^Tx $$

$$ \Theta 表示 parameters $$

### **策略**

$$ \min_{\Theta}\frac{1}{2}\sum_{i=1}^m (h_{\Theta}(x^{(i)})-u^{(i)})^2 $$

let $$J(\Theta) = \frac{1}{2}\sum_{i=1}^m (h_{\Theta}(x^{(i)})-u^{(i)})^2 $$
=> 

$$ \min_{Theta} J(\Theta) $$

### **算法**

#### **Batch Gradient Descent:**

第i个特征
?
$$ \Theta_i := \Theta_i - \alpha \frac{d}{d\Theta_i}J(\Theta) $$

$$ \Theta_i := \Theta_i - \alpha \sum_{j=1}^m ( h_{\Theta} ( x^{(j)}) - y^{(j)} ) * x_i^{(j)} $$

> 板书时
> a:=b 把b赋给a
> a=b 进行真值判断


#### **SGD**


Repeat {    
  For j=1 to m {

  $$ \Theta_i := \Theta_i - \alpha ( h_{\Theta} ( x^{(j)}) - y^{(j)} ) * x_i^{(j)} $$	
  
  }   
}   



$$ \nabla_{\theta}  $$


$$ \nabla_A{f(A)} $$


$$ trA $$


**Fact:**
  trAB = trAB
  trABC = trCBA = trBCA
  ...


--------

class 2




过拟合 overfitting

二维x-y样本点，用不同阶的函数进行拟合，阶数过低出现underfit， 阶数过高可能出现overfitting

参数选择

- PCA?


**参数学习算法 parametric learning algorithm**   

- fixed set of parameters


**非参数学习算法 non-parametric learning algorithm**   

- \# of parameters grows with m(size of train set)
- 即使训练后，算法依然需要跑遍整个训练集
- KNN?
- locally weighted regression


**locally weighted regression/Loess**

- 不用考虑特征选择?
- 属于非参数学习算法，每次predict都要遍历整个训练集
- **对要预估的x附近的训练集进行linear regression,之后基于这个linear regression对x进行predict**
- linear regression时的每个样本点和x的平方差的权重是基于样本点的x的距离关于高斯函数计算出来的

$$ w^{(i)} = exp(-\frac{(x^{(i)} - x)}{2{\tau} ^2}) $$

- 被用于了直升机的控制





## Probabilistic interpretation of Linear Regression 

**从概率论说明使用最小二乘法的作为linear regression的策略理由**
**证明极大似然估计在误差是高斯分布和IID的假设下，等价于最小二乘法**

```c
/*
            1                 2                 3
 风险最小化 ---> 经验风险最小化 ---> 极大似然估计  ---> 最小二乘法
                            | 4                 5
                            ----> 结构风险最小化 ---> 贝叶斯估计的最大后验概率估计
           
 1. 联合概率未知，数据量大， 易过拟合
 2. 模型是**条件概率分布** $$P(Y\|X)$$，损失函数是**对数损失函数**
 3. 极大似然估计在误差是高斯分布和IID的假设下，等价于最小二乘法
 4. 经验风险最小的**正则化**（添加模型复杂度惩罚项），不易过拟合 
 5. ?
*/
```
assume $$y^i = \Theta^T x^{(i)} + \epsilon^{(i)} $$

=>

$$\epsilon^{(i)} = y^i - \Theta^T x^{(i)} $$

其中 $$\epsilon$$表示error，符合**高斯分布**。 $$\sigma 表示$$ deviation。

$$P(\epsilon^{(i)}) = \frac{1}{\sqrt{2\pi} \sigma} exp( - \frac{(\epsilon^{(i)}^2)}{2\sigma^2} ) $$

=>

$$ P(y^{(i)} | x^{(i)};\Theta) $$

$$ = \frac{1}{\sqrt{2\pi} \sigma}  exp(-\frac{ (y^{(i)} - \Theta^Tx^{(i)} )^2 }{2\sigma^2} ) $$

$$ y^{(i)}| x^{(i)};\theta ~ N(\Theta^Tx^{(i)}, \sigma^2) $$


> theta前的；说明theta不是作为随机变量，而是作为这个概率函数的已知参数



**似然函数**

$$L(\Theta)$$表示$$\Theta$$的似然函数

- the probability of data Y given X and prioritized by theta. 
- 和概率很接近，也很容易混淆
- 似然函数主要强调 Y概率是以$$\Theta$$为参数的函数
- likehood of the parameter ($$\Theta$$)
- probability of the data ()


$$ P(\epsilon^{(i)}) = P(y^{(i)} | x^{(i)};\Theta)$$

开始证明：

**似然函数** 

$$ \color{red}{  L(\Theta) = P(Y | X;\Theta)  }$$

error term are **IID(independentlly identically distributed  )**

（m training examples were generated independently）

$$= \prod_{i=1}^m P(y^{(i)} | x^{(i)};\Theta) $$

assume $$\epsilon$$ 的各个分量$$epsilon^{(i)}$$的误差项服从相同的高斯分布

$$=  $$ 


**极大似然**: 选择参数$$\Theta$$ 使得数据出现的可能性尽可能大。

choose theta to maximize L(theta) = P(Y\|X,theta)

为了数学上的便利，定义 log likehood function

$$ l(theta) = logL(theta)$$

$$= \sum_{i=1} log(\frac{1}{} ) $$

=> max l(theta)等价于 最小化 J

=> 最小化 J() ,也就是最小二乘法


证明完毕. 

注意： 在证明中 假设error是 高斯分布， IID。


## logistic regression 

classification 

$$ y \in {0,1} $$

- linear regression并不适用于分类 classification

g(z)
```c
    /--
   /
--/
```

$$z = theta^T x$$


### **算法**

- gradient ascent

- newton's method

$$ 求 f(\Theta) = 0 的根$$

$$ \Theta^{(t+1)} = \Theta^{(t)} - \frac{ f(\Theta^{(t)}) } { f^{'}(\Theta^{(t)}) } $$

类似的，找对数似然函数的极值: $$ l(\Theta) \quad with \quad \Theta \quad such \quad that \quad l^{'}(\Theta) = 0 $$

$$ \Theta^{(t+1)} = \Theta^{(t)} - \frac{l^{'}(\Theta^{(t)})}{l^{''}()\Theta^{(t)}} $$

除了对logistic regression，牛顿法对于其他GLM的效果都不错？

如果$$\Theta$$是向量：

$$ \Theta^{(t+1)} = \Theta^{(t)} - H^{-1}\nabla $$

- 较少迭代次数

## perceptron 

g(z)
```c
  __
  |
__|

```
$$z = theta^T x$$



class 4:

```c
Logistic Regression
- Newton's Method 见上一节笔记

Exponential Family

Generalized Linear Model(GLMs)

```


## Generalized Linear Model(GLMs)

目前为止讲了两个
$$P(y|x,\Theta)$$的模型

$$ y \in R, Gaussian --> Least Square Regression(Linear Regression?) $$

$$ y \in \{0, 1\}, Bernoulli --> Logistic Regression $$

以下将说明上述两个模型都是GLM的特例。


### The exponential family

- Bernoulli Dist
- Gaussian Dist

#### **The exponential family 的通用公式**

$$ p(y;\eta) = b(y)exp(\eta^T T(y) - a(\eta) ) $$

其中:

$$ \eta$$ : natural parameter of the distribution 

$$ T(y) $$:**sufficient statistic**? (大多数情况: $$T(y) = y$$)

$$ a(\eta) $$:log partition function

**一组$$T,a,b$$决定一组分布, 这组分布以$$\eta$$为参数得到某一个具体的分布。**

以下证明  Bernoulli Dist 和 Gaussian Dist 都可以由GLM的通用公式推出

##### **Bernoulli Dist**

$$Ber(\phi) p(y=1;\phi) = \phi $$

GLM的形式

$$p(y;\phi) = \phi^y(1-\phi)^{1-y}$$

$$exp((log(\frac{\phi}{1-\phi})))y + log(1-\phi) $$

==>

$$ p(y;\eta) = b(y)exp(\eta^T T(y) - a(\eta) ) $$

其中

$$ T(y) = y $$

$$ a(\eta) = log(1+e^{\eta}) $$

$$ b(y) = 1 $$




##### **Gaussian Dist**

let $${\sigma}^2 = 1$$

$$p(y;\mu) = \frac{1}{\sqrt{2\pi}} exp(-\frac{1}{2}(y-\mu)^2)$$ 

GLM的形式

$$ p(y;\mu) = \frac{1}{\sqrt{2\pi}} exp(-\frac{1}{2}y^2)  exp(\mu y - \frac{1}{2}{\mu}^2 ) $$

==>

$$ p(y;\eta) = b(y)exp(\eta^T T(y) - a(\eta) ) $$

其中

$$ \eta = \mu $$

$$ T(y) = y $$

$$ a(\eta) = {\eta}^2/2 $$

$$ b(y) = (1/\sqrt{2\pi})exp(-y^2/2) $$


##### **其他的The exponential family**

- **The multinomial** (which we’ll see later), 
- **Poisson Distribution** 
  - (for modelling count-data; number of visitor)
  - 在特定情况下(天气，广告投放情况，节假日，促销等)， 网站/店铺的访问量 
- **the gamma and the exponential** 
  - (for modelling continuous, non-negative random variables, such as timeintervals); 
- **the beta and the Dirichlet** (for distributions over probabilities);




### 如何构建 Generalized Linear Model(GLMs)


统计学习三要素(模型，策略，算法) 之 **模型**

**Assume or Design choices:**

1. y\|x;\theta ~ ExpFamily
2. Given x, goal is to output E[T(y)\|x],  want  h(x) = E[T(y)\|x]. mostly T(y) = y **==>** h(x) = E[y\|x]
3. $$ \eta = \Theta^T x $$ ?


#### Multinomial

$$ T(y)!=y $$

$$y \in {1,...,k}$$

Parameters, \Pi_1



??

