---
layout: post
title:  "Ray Tracing:  Whitted Integrator"
date:   2017-7-4 23:1:38 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2017.7.3   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


# 介绍

## 光线追踪算法简介

最近在学习PBRT(Physically Based Rendering: From Theory to Implementation)的相关资料，看到了Whitted Integrator，研究了下其算法，发现和当初在CMU上CG课做的课程大作业非常类似。当初时间太赶，没怎么花时间研究，于是便有了此文。

光线追踪在Computer Graphics中是所谓圣杯般的存在。现在的大多数应用（游戏，CAD等）都是非光线追踪(局部光照)加各种trick来补偿光线追踪（全局光照）做的渲染。光线追踪一般是指由屏幕像素发射光线，去和场景递归求交着色，追踪求得该像素的颜色值。虽然可以得到很逼真的结果，但是由于计算量大（主要是求交计算），因此实际应用不多。近年来随着GPU的日益强大，特别是Nvdia发布了基于GPU的光线追踪渲染SDK [OptiX](https://developer.nvidia.com/optix), 基于光线追踪来渲染的应用预料将日益增多。 本文将介绍Whitted光线追踪算法。

Integrator是指[渲染方程](https://en.wikipedia.org/wiki/Rendering_equation)？。而对于渲染方程的各种数值求法，衍生出各种Integrator。

## Whitted Integrator

> Whitted’s algorithm simplifies this integral by ignoring incoming light from most directions and only evaluating Li(p, ωi) for directions to light sources and for the directions of perfect reflection and refraction.   
>---- PBRT-v2 p12

Whitted Integrator包括两个部分，Direct 和 Indirect。Direct是在hit point向所有光源发射**shadow ray**和场景求交判断遮挡关系后直接shading。Indirect是指向镜面反射方向和镜面折射方向分别发射一条reflected ray和 refracted ray 进行recursive 追踪。具体见下图，来自 [^3]。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-7-4-whitted-0.png)

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-7-4-whitted-1.png)


# 参考代码

PBRT-v3的whiited integrator的代码[^2]。
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-7-4-whitted-2.png)

50 相机发射的primary ray和场景物体相交测试，如有交点，继续，否则遍历点光源   
62 确定使用哪种bsdf    
67 如果是自发光物体，计算Le    
69~80 (Direct)对所有光源（点光源，面光源，环境光）采样，发射shadow ray，若无遮挡则计算直接光照。   
81~85 (Indirect)如果未超过最大深度，追踪 镜面反射 和 镜面折射 的光线。   

OptiXSDK中也有Whitted光线追踪算法的代码[^4]。

# 实现
todo

# 参考

[^0]: 光线跟踪算法技术 chapter24 镜面反射 chapter27 透明度（折射）   
[^1]: PBRT-v2 1.3.6 AN INTEGRATOR FOR WHITTED RAY TRACING    
[^2]: PBRT-v3 1.3.5 AN INTEGRATOR FOR WHITTED RAY TRACING （推荐，和本文代码一致）   
[^3]: UT-Austin's Computer Graphics Course Note on Ray Tracing [`LINK`](https://www.cs.utexas.edu/users/fussell/courses/cs384g-fall2011/lectures/lecture09-Ray_tracing.pdf)   
[^4]: OptiXSDK sample:  OptiXWhitted - whitted's original sphere scene [`LINK`](https://developer.nvidia.com/optix)    

