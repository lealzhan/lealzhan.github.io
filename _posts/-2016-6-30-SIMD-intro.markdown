---
layout: post
title:  "编程：SIMD简介"
date:   2016-6-30 12:04:00 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - parallel computing
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
2016.6.30       

## Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## 介绍

todo
   
## Code

``` c++ 

//代码示例
#include <xmmintrin.h>    //128bit
#include <emmintrin.h>   //256bit, 同时包含 128bit(xmmintrin.h)

//定义一个16个byte（128bit or 4 float）对齐的数组
__declspec(align(16)) float A[]={2.0f, 1.0f, 3.0f,4.0f}; // 16-->32,  float-->double
__declspec(align(16)) float B[]={2.0f, 1.0f, 3.0f,4.0f}; // 16-->32,  float-->double
__declspec(align(16)) float C[]={0.0f, 0.0f, 0.0f,0.0f}; // 16-->32,  float-->double

//将该数组传递给__m128
__m128 a = _mm_load_ps(&A[0]); //__m128d, _mm_load_pd
__m128 b = _mm_load_ps(&B[0]); //__m128d, _mm_load_pd
__m128 c; //__m128d

//进行numb次加法计算
for(int i=0;i<numb;i++){
	c = _mm_add_ps(a,b);
}

//将__m128传递给数组C
_mm_store_ps(&C[0],c);
//打印
std::cout<<C[0]<<C[1]<<C[2]<<C[3]<<std::endl; 
```


