---
layout: post
title:  "编程：CUDA NOTE"
date:   2015-8-17 00:47:38 +0800
categories: jekyll update
---
# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


关于GPU的提速效能
----------------
基于这篇文章, GPU对于求解线性方程组常用的preconditioned Conjugate Gradient method的提速性还不到一个数量级，而且用于比较的CPU只有双核。再考虑到编程的复杂性，可见在求解线性方程组方面，GPU相对于CPU并没有什么革命性的突破，充其量只是NVDIA的一个噱头。下图来自 CUDA 6.5 Performance Report (September 2014) ⇨只有5X的提速。。。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2015-8-17-CUDA-NOTE-0.png)

Double Precision for GPU
-----------------------
还不是太清楚GPU是怎么在硬件上处理双精度的（待更新）。 从CUDA 6.5 Performance Report可以看出，当前双精度的处理能力只有单精度的一半左右（早期更低）。但是科学计算需要双精度。

有篇文章提出单双混合精度 共轭梯度来最高效率的用GPU来解决有限元这类的科学计算[Zhang, Jianfei, 2013]。


Thrust
-------
functor(仿函数)在thust中是个很有用的东西。transform, transform_reduce等，都以functor定义对大量元素的并行操作。


cuSPARSE
--------
cuSPARSE用于sparse matrices的线性方程组求解。与CUSP基于THRUST不同，cuSPARSE是直接基于cuda runtime的，所以其具有更强的独立性，不会受制于THRUST的发展版本，但是使用起来更加繁杂。


cuBLAS
-------
To use the cuBLAS API, the application must allocate the required matrices and vectors in the GPU memory space, fill them with data, call the sequence of desired cuBLAS functions, and then upload the results from the GPU memory space back to the host.

CUSP (out of date)
-----------------
CUSP is very easy to use, algorithms like conjugate gradient, have already been implemented, and can be used just by call it in one line. 
CUSP is based on Thrust, which means you have to include Thrust library of the right version. However, CUSP is not supported anymore, which can cause lots of compiling problems.

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2015-8-17-CUDA-NOTE-1.JPG)

MAGMA
-----
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2015-8-17-CUDA-NOTE-2.png)


CUDA VS 2010 32 bit Setup
-------------------------
Win7， Quadro K1000M，VS2010， CUDA 6.5
1. 生成自定义
CUDA runtime
2.  链接器 Linker 附加库目录：$(CudaToolkitLibDir);
附加依赖项
cublas.lib
cusparse.lib
cudart_static.lib
kernel32.lib
user32.lib
3.  可能还需要添加以下如今到VC++包含目录，以便helper_funcitons.h调用
C:\ProgramData\NVIDIA Corporation\CUDA Samples\v6.5\common\inc

