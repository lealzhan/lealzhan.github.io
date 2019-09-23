---
layout: post
title:  "光线追踪:OptiXSDK optixDynamicGeometry"
date:   2017-9-9 20:1:32 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2017.9.9   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


# 介绍

> optixDynamicGeometry compares accel structures for a dynamic scene. A multi level BVH allows faster updates at the cost of slower ray intersections vs. a single level BVH.
> --- OptixSDK sample: optixDynamicGeometry


本文主要是对OptixSDK的例子optixDynamicGeometry[^0]做一下分析。 这个例子主要是比较了一下Optix提供的两种主要的加速结构的组织模式
multi-acceleration mode - SeparateAccelsLayout    
single-acceleration mode - RebuildLayout


# 源码分析：

**RebuildLayout**: single-acceleration mode. slower updates, faster ray intersections.    
导入mesh时，直接apply transformation到mesh的原始数据上，将mesh和GeometryInstance一一对应后，直接将GeometryInstance挂在geometryGroup下(top_obj).   

```c++
         GeometryGroup  -- acceletation structure
                   |   
            /               \
           /                 \
GeometryInstance     GeometryInstance

mesh0-transformed   mesh1-transformed
```


**SeparateAccelsLayout**: multi-acceleration mode. faster updates, slower ray intersections.     
导入mesh，不作用transformation，而是将其和GeometryInstance一一对应，将GeometryInstance挂载在GeometryGroup下， 再将GeometryGroup挂载在Transform节点下，将Transform节点挂载在Group下(top_obj).

```c++
           Group  -- acceletation structure   
            |   
          /   \   
        /      \
Transform       Transform    
     |              |    
GeometryGroup    GeometryGroup      -- acceletation structure for each geometry group
    |               |
GeometryInstance  GeometryInstance 

mesh0              mesh1   
```


# 结果测试：

A: ray trace when changing camera:（加速结构遍历）        
B: geometry transformation(加速结构更新+遍历)

single-acceleration mode: A: 30fps B:10fps    
multi-acceleration mode: A: 20fps B:20fps    

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-9-9-optix-optixDynamicGeometry-0.png)

# Reference

[^0]: optixDynamicGeometry [`Code`](https://pan.baidu.com/s/1gfrgRXh)