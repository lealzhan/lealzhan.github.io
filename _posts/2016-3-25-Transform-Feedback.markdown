---
layout: post
title:  "变换反馈"
date:   2016-3-25 20:47:38 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - computer graphics
  - opengl
  - rendering
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false

---

詹令   
lealzhan@126.com    
2016.3.25       

## Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## 什么是变换反馈

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-3-25-Transform-Feedback-3.png)

## 基于变换反馈的粒子系统

opengles 3.0 chapter14介绍了基于变换反馈的粒子系统。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-3-25-Transform-Feedback-0.png)

定义两组着色器，一组用于变换反馈，一组用于正常渲染。
定义两个顶点缓存，交替作为Array_Buffer和Transform_FeedBack_Buffer。

基于变换反馈的粒子系统 总的来说：
就是一组VertexBuffer数据经过变换反馈处理（发射粒子）后，结果存储在另一组VertexBuffer上，这组VertexBuffer再拿去做正常的渲染（更新粒子位置和渲染）。

基于变换反馈的粒子系统 更细节地：
VertexBuffer数据是 pos0_velocity0 pos1_velocity_1 ...
在进行变换反馈的时候，传入Vertex Shader的数据是 pos0_velocity0。
那我们就可以并行地在shader中对a_pos0 和 a_velocity0 进行处理， 输出v_pos0 和 v_velocity0。而输出的数据将会被写入另一组VertexBuffer中。这组VertexBuffer将会被用于正常渲染。

从这个粒子系统的例子推广到一般情况，transform feed back的作用就是**用GPU对GPU内存上的VertexBuffer的数据进行并行处理（一组VB处理后写入另一组VB），而无需传回主机内存用CPU进行处理**。这样可以更高效率的进行VertexBuffer的数据处理。

这样的话，渲染到Vertex Buffer（R2VB）也就很好理解了。一组VB处理后写入另一组VB，transfer feed back 可以理解成一个render的过程。


## 变换反馈和细分曲面着色器的差别

VB_0 -> tf(vertex shader) -> VB_1 -> normal render(vertex shader)  -> normal render(fragment shader)    
VB_0 -> normal render(vertex shader)  ->   tessetelation shader  ->      normal render(fragment shader)

这两个VB应该是一模一样的（大小类型）（？）。
而tessetelation shader的话，意味着我们只需要一组VB，shader会自己分配内存，添加新的数据点。

## 猫咪毛发的渲染原理

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-3-25-Transform-Feedback-2.jpg)

这个例子[^1]<sup>,</sup>[^2]来自[Imagination Technologies](https://www.imgtec.com/)， 主要是用于展示OpenglES 3.0的新特性。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-3-25-Transform-Feedback-1.png)

Execute Once 的那部分就是 Transform Feedback的过程。一组VertexBufer（ Cat Geometry）经过处理骨骼动画权重的Vertex Shader后存储到另外一组Vertex Shader（Cat Animated Pose）中。这个过程十分费时，因为作者说猫咪每个顶点的骨骼绑定为12个（一般情况为3个）。而右边的render passes会被执行很多次，因为猫咪的毛是由一层层纹理堆叠起来的。如果左边的骨骼变换无法和右边的渲染分离开的话，将会十分缓慢。

## Reference

[^1]: Soft Kitty: An overview of the latest OpenGL ES 3.0 technical demo from Imagination [`LINK`](https://www.imgtec.com/blog/soft-kitty-overview-latest-opengl-es-3-0-tech-demo-imagination/)    
[^2]: OpenGL ES 3.0 – Creating the Soft Kitty Demo [`PDF`](http://cdn.imgtec.com/idc14-china/7-PowerVR-IDC14-OpenGLES3.0-CreatingTheSoftKittyDemo-Nov14-v2-FINAL.pdf)   



