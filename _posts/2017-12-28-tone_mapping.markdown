---
layout: post
title:  "tone mapping"
date:   2017-12-28 20:1:32 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - computer graphics
  - computer vision
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
201.12.28   

# Contents 
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


# Tone Mapping


- **动态范围 dynamic range**：图像的**最明亮区域**与**最暗区域**的光亮度对比度。
- 例如，阴暗处的亮度约为10～cd／m2，而太阳光直射下的区域亮度可达到10^5cd／m2，也就是此场景动态范围为**10000：1** 
- 一般CRT显示器的最大显示亮度约为100cd／m2， 即显示的动态范围约为**100：1** 
- **色调映射(Tone Mapping)** 将高动态范围图像数据通过某种**映射算子**处理成低动态范围设备所能接受的动态范围内。
- **色调映射** = **Tone Mapping** = **色调映射算子** = Tone Mapping Operator  **TMO**

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-28-tone-mapping-0.png)


![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-28-tone-mapping-1.png)




# 游戏中常用的几种Tone Mapping 方法

``` c
//
// tone mapping: hdr(0~infinite) to ldr(0~1)
//

//http://filmicworlds.com/blog/filmic-tonemapping-operators/
vec3 Uncharted2Tonemap(vec3 x)
{
	float A = 0.15;
	float B = 0.50;
	float C = 0.10;
	float D = 0.20;
	float E = 0.02;
	float F = 0.30;
    return ((x*(A*x+C*B)+D*E)/(x*(A*x+B)+D*F))-E/F;
}

vec3 Uncharted2TonemapMain(vec3 texColor, float TonemapMaxWhite)
{
	vec3 color = Uncharted2Tonemap(texCOlor)/Uncharted2Tonemap(vec3(TonemapMaxWhite));
	return color;
}

vec3 ReinchardEq3Tonemap(vec3 x)
{
	return x / (1.0 + x);
}

vec3 ReinchardEq4Tonemap(vec3 x, float white)
{
	return x * (1.0 + x/white)/(1.0 + x);
}
```
``` c
//调用
vec3 tone_mapping(vec3 hdr_color, int mode = -1)
{
	
	vec3 mapped = vec3(0.0);	
	float TonemapMaxWhite = 1.8;
	float exposure = 3;

	if(mode==0) //Reinhard tone mapping
	{
		mapped = ReinchardEq3Tonemap(hdr_color*exposure);
	}
	else if(mode ==1)
	{
		mapped = ReinchardEq4Tonemap(hdr_color*exposure, TonemapMaxWhite);
	}
	else if(mode ==2) //Exposure tone mapping
	{
		mapped = vec3(1.0) - exp(-hdr_color*exposure);
	}
	else if(mode ==3) //Uncharted2Tonemap
	{
		mapped = Uncharted2TonemapMain(hdr_color*exposure, TonemapMaxWhite);
	}
	else if(mode==4) //only scale, no tone mapping
	{
		mapped = hdr_color*exposure;
	}
	else
	{
		mapped = hdr_color;
	}

	return mapped;
}
```

# 一些较为复杂的Tone Mapping


## 基于分离压缩思想

有以下几个步骤
1. 将HDR分离Decompose成Base Layer（HDR）和Detail Layer（LDR）
	- HDR的luminance(或其log)经过filter得到Base Layer
	- HDR的luminance (或其log)和Base Layer相减，得到Detail Layer

2. 对Base Layer（HDR） 压缩成LDR，对Detail Layer （LDR）基本不压缩
3. 然后再将处理后的两个Layer（都是LDR）合成最后的结果（LDR）。

以下示意图是对上述步骤的具体展示（来自Durand02的PPT）。
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-28-tone-mapping-3.png)

使用此算法应注意halo辉光现象。这是由于在decompose时使用了不保边的滤波器造成的。 参照了一些论文，基于这个思想的有以下几种常见算法：
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-28-tone-mapping-4.png)

### Durand02

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-28-tone-mapping-2.png)



# Reference

## 书籍
Reinhard E, Ward G, Pattanaik S, et al. High Dynamic Range Imaging - Acquisition, Display, and Image-Based Lighting (2. ed.).[M]// Political parties and the state :. Princeton University Press, 2010:2039 - 2042.

## 论文

Chiu K, Herf M, Shirley P, et al. Spatially Nonuniform Scaling Functions for
High Contrast Images[C]// In Proceedings of Graphics Interface ’93.
1993:245--253.

Tumblin J, Turk G. LCIS:a boundary hierarchy for detail-preserving contrast
reduction[C]// Conference on Computer Graphics & Interactive Techniques.
1999:83-90.

Durand F, Dorsey J. Fast bilateral filtering for the display of high-dynamicrange images[J]. Acm Transactions on Graphics, 2002, 21(3):257-266.

Farbman Z, Fattal R, Lischinski D. Edge-preserving decompositions for
multi-scale tone and detail manipulation[C]// Acm Siggraph. ACM, 2008:1-
10.
