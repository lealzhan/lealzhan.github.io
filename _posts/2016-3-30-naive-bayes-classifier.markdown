---
layout: post
title:  "朴素贝叶斯分类"
date:   2016-3-30 7:1:32 +0800
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
2016.3.30   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

机器学习   
分类   
朴素贝叶斯分类   

-------------------------------------------------------------

# 基本概念

## 朴素贝叶斯决策理论的核心思想
选择具有最高概率的决策

## 条件概率
计算条件概率的两种方法

### 条件概率计算公式

$$ P(X|C) =  \frac{P(X and C)} {P(C)} $$

### 贝叶斯准则

$$ P(C|X) =  \frac{P(X|C)P(C)}{P(X)} $$

## 朴素贝叶斯分类/Naive Bayes classifier

![](https://4.bp.blogspot.com/-a5kVHCQDxCM/Vvueh_DHzuI/AAAAAAAAHZg/1q7rfFgLfakuY1Z8cXb-Z5ubJAqzg_Z3A/s1600/PIC_20160330_173216_E0B.JPG)

### 贝叶斯分类
### 朴素贝叶斯分类
**朴素 naive**：各个特征之间相互独立的。   

朴素贝叶斯是贝叶斯分类器的一个扩展，是用于文档分类的常用算法。

 
#### 朴素贝叶斯分类的一般过程

- 收集数据。
- 准备数据。需要数值或布尔类型数据
- 分析数据。有大量特征时，绘制特征作用不大，此时使用直方图效果更好。
- 训练算法。计算不同**独立特征**的条件概率。
- 测试算法。计算错误率。
- 使用算法。朴素贝叶斯分类的一个常见的应用是文档分类，但是可以在任意场景中使用朴素贝叶斯分类。  

## 实例

### 文档的自动分类

聊天信息，电子邮件，新闻，用户留言 

#### 准备数据：从文本中构建词向量

- 词汇表： 
- 词向量：利用词汇表，对某一个输入文本（一组单词）的每一个单词进行存在性判断以生成的一个0-1向量

``` python
todo
```

#### 训练算法： 从词向量计算概率

$$ p(c_i|w) =  \frac{p(w|c_i)p(c_i)}{p(w)} $$

其中：
$$ p(c_i|w) $$ : 一个文本对应一组词向量$$w$$，那么其属于$$c_i$$类的概率是多少      
$$ p(c_i) $$ : $$c_i$$出现的概率   
$$ p(w) $$ : $$w$$出现的概率   
$$ p(w|c_i) $$   
基于朴素贝叶斯的独立性假设，所有特征是相互独立的，则：   
$$p(w|c_i) = p(w_0|c_i)p(w_1|c_i)p(w_2|c_i)...p(w_N|c_i)$$   

$$ p(c_i|w) =  \frac{p(w_0|c_i)p(w_1|c_i)p(w_2|c_i)...p(w_N|c_i)p(c_i)}{p(w)} $$



need to read src code...


# Reference
- [[阮一峰blog](http://www.ruanyifeng.com/blog/2013/12/naive_bayes_classifier.html)] 入门介绍   
- 机器学习实战 chapter4
- Tom Chapter 6 不理解


