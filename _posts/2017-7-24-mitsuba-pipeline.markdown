---
layout: post
title:  "Mitsuba Pipeline"
date:   2017-7-24 00:47:38 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2017.7.24   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


# 关于Mitsuba #

Mitsuba[^0]是一款开源的渲染器，在计算机图形学的学术界很流行。本文将大体其介绍其光线追踪部分的主要渲染流程。 

# 光线追踪的渲染流程

图A给出了从屏幕像素到renderblock的流程。   

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-7-24-mitsuba-pipeline-0.PNG)
图A

得到renderblock的值之后，要经过特定的reconstruction filter处理。 常见的reconstruction filter有box filter, gaussian filter等。 之后如果是输出到hdr图像文件，则直接写入float的颜色值，如果是输出ldr图像文件或者渲染到屏幕上则还要经过特定的tone mapping(Reinhard等)和Gamma Correction。

# Reference #
[^0]: Mitsuba Official Site[`LINK`](http://www.mitsuba-renderer.org/)
