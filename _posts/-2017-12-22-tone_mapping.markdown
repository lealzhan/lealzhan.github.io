---
layout: post
title:  "tone mapping"
date:   2017-12-22 7:1:32 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - computer graphics
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
2017.12.22   

# Contents 
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


- Tone mapping or tone reproduction
- **Tone mapping**: Fitting a wide range of illuminaion levels to within the screen;s limited gamut. [0]
  - [0, infinity] => [0,1]
 
- non-adaptive and adaptive tone mapping operator

**Human Visual System HSV**


# **色彩知觉的形成**

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-2.png)

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-3.png)


## **Spectral Power Distribution SPD**

- 用于描述**光,光源**
- $$S({\lambda})$$   
- **SPD** of a light defines the power of light at each wavelength


![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-1.png)


http://www.lrc.rpi.edu/education/learning/terminology/spectralpowerdistribution.asp

## **Spectral Refletance 光谱反射率**

- 用于描述 **被观察物体**

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-4.png)


## **Spectral Radiance$$L(\lambda)$$**



## **Spectral Response Curve**
**or Spectral Matching Curve or Cones Spectral Sensitivity $$V(\lambda)$$**
**配色函数 color matching function**

- 用于描述**视觉系统**
- describes the relative sensitivity of the human eye to various wavelengths. 

### LMS?


### CIE1931-RGB



### CIE1931-XYZ

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-0.png)



$$ x_{\lambda} = \int_{\lambda} S(\lambda) X(\lambda) $$

$$ y_{\lambda} = \int_{\lambda} S(\lambda) Y(\lambda) $$

$$ z_{\lambda} = \int_{\lambda} S(\lambda) Z(\lambda) $$

其中   
$$S(\lambda)$$表示Spectral Power Distribution   

$$X(\lambda)$$，$$Y(\lambda)$$， $$Z(\lambda)$$   
表示Spectral Response Curve

$$Y(\lambda)$$表征 亮度值，$$X(\lambda)$$，$$Z(\lambda)$$ 表征 色彩?

### **光谱光视效率**
[4]   
$$V(\lambda)$$   
光谱光视效率 $$V(\lambda)$$ 和$$Y(\lambda)$$存在线性关系

### **Luminance Y** 

$$ Y = \int _\lambda L(\lambda) V(\lambda)d\lambda $$


光谱光视效率 $$V(\lambda)$$ 和$$Y(\lambda)$$存在线性关系
$$V(\lambda) = 683Y(\lambda)$$

=>

$$ Y = 683\int _\lambda L(\lambda) Y(\lambda)d\lambda $$



**luminance vs. radiance**
**亮度 vs. 辐射度**

luminance 是人眼相关的$$V(\lambda)$$。luminance由radiance和人眼响应曲线积分得到。
radiance 和人眼无关，是物理绝对的。







## global tone mapping operator

### Maximum to white

``` python
整个屏幕的最亮一点的颜色 max
for each pixel on the screen 
  color = color/max
```

缺点：一个异常的非常亮的点，会使处理过的整个屏幕都非常暗。





### Contrast-based scale factor


### Varying adaptation luminance







## local tone mapping operator

### Spatially-varying non-linear scale


# Reference
[0] Real-time rendering 10.11 tone mapping    
[1] PBRT v1 [pdf] [code]
[2] High Dynamic Range Imaging: Acquisition, Display, and Image-Based Lighting  [[LINK](https://pan.baidu.com/s/1dE5lvDR)]
[3] Introduction to the CIE Color Specification System CIE 表色系統介紹, 孫沛立, 2003 [PPT]
[4] 色彩学第五章(CIE色度系统) [[LINK](https://wenku.baidu.com/view/553102cfda38376baf1fae50.html)]

[5] https://wenku.baidu.com/view/0c54ebccda38376baf1fae31.html

NOTE:
PBRT v2 v3 取消了 tonemapping直接输出hdr格式的图片。PBRTv1有tonemapping。PBRTv3建议关于tonemapping(HDRtoLDR)的课题读High Dynamic Range Imaging: Acquisition, Display, and Image-Based Lighting




![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-2.png)

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-12-22-tone-mapping-3.png)

光源 -- 由**SPD**描述   

被观察物体 -- 由**光谱反射率** $$R(\lambda)$$ %描述   

视觉系统 -- 以**配色函数 color matching function**描述   
- CIE1931-RGB, CIE1931-XYZ
- LMS？


