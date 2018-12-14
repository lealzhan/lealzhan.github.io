---
layout: post
title:  "three.js CLoth 算法"
date:   2018-12-14 7:1:32 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2018-12-14    

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# Intro

three.js => [Cloth.js](https://github.com/mrdoob/three.js/blob/master/examples/js/Cloth.js)

# 布料模拟算法

## 初始化

约束状态

## 更新

受力 => 积分 => 添加约束


### 受力

a = sumF/m


### 积分

velvet积分

泰勒展开

    x(t+dt)
    x(t-dt)

相减

    x(t+dt) = 2*x(t) - x(t-dt) + a*dt*dt

==>?

实际使用

    x(t+dt) = (1-damping)*x(t) + x(t) + a*dt*dt




### 约束

#### 内部约束

##### particle distance

	d = p2 - p1
    half_correction = 0.5 * d * (1 - ||d0||/||d||)
    p1 = p1 + half_correction
	p2 = p2 - half_correction

##### particle shear


##### particle bend



#### 外部约束

##### floor

    if(x<0) x=0


# Code








