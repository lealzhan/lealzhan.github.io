---
layout: post
title:  "空间数据结构"
date:   2017-10-21 20:13:15 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - algorithm
  - computer graphics
  - rendering
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false

---

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# **Null Accelerator**

光线和场景里的所有primitives遍历求交。


## Build


## Traversal

计算复杂度 
O(n)

# **Regular Grid Accelerator**

空间被划分为相同大小的不重叠的格子(voxel), 每个voxel保存和它重叠(overlap)的primitives . 光线和场景求交时，按顺序由近及远依次经过grid，只和grid相关联的primitives进行求交计算，一旦相交便停止。这样和光线距离较大的primitives便不用被考虑，求交计算量大大减少。

- 缺点:  如果场景的primitives分布分不均匀， 求交效率便会降低。 BVH和kdtree便可以适应这种primitives分布不均匀的情况。

## Build


## Traversal



# **Bounding Volume Hierachies BVH**

每个primitive计算其bounding volume, 再把他们组织成一个hierarchy。

- 优点: 每个primitive只出现在BVH一次。因此最多只被进行一次相交测试. 相对的，grid结构中，一个primitive可能被多个grid相关联，因此可能被进行多次相交测试. 另外 这也使 BVH对于内存的使用有个上限(BOUNDED)。 构建速度和grid几乎一样快，但是遍历的效率比grid高。kdtree遍历效率比BVH稍高，但是需要更多的时间去构建。 BVH比kdtree更加 numerically robust， 不易受到数值误差(round off)的影响。

## Build
三种算法
- split sah (surface area heuristic) :  默认 构建时间略长，遍历速度快。 
- split middle:
- split equal counts

三个阶段:
1. 为每个primitive计算bounding volume，并存储在一个数组中。
2. 递归地将primitive划分，最终构建成一棵树.
3. 

## Traversal

# **空间二分树 Binary Space Partition BSP**

## Build


## Traversal


# **KD tree**

Kd-树就是一种平衡二叉树. k-d树是一种空间划分树:将空间通过平面递归的划分.kd树是一种特殊的空间二分树.

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-10-21-spatial-data-structure-0.PNG)

## Build


## Traversal

https://baike.baidu.com/item/kd-tree/2302515?fr=aladdin
http://blog.csdn.net/so__sunshine/article/details/52613841
http://blog.csdn.net/bugrunner/article/details/5889294

# Reference

[0] PBRT v2 CH4
[1] http://blog.sina.com.cn/s/blog_530fef1e01015ju8.html