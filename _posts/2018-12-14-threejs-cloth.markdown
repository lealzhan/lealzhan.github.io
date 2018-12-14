---
layout: post
title:  "three.js CLoth 算法"
date:   2018-12-14 7:1:32 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## Intro

three.js => [Cloth.js](https://github.com/mrdoob/three.js/blob/master/examples/js/Cloth.js)

## 布料模拟算法


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

    if(x<0) x=0




场景配置文件   
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2018-3-2-xyrender-1.png)




