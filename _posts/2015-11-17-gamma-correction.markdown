---
layout: post
title:  "实时渲染：伽马校正 Gamma Correction"
date:   2015-11-17 20:47:38 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2015.11.17    
last modified 2017.6.20    

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


# 1.基本原理
---


不解释什么是gamma，gamma correction，先了解下以下几个事实：

```
本文假定 2.2=2, 1/2.2=0.5 (assume: 2.2=2, 1/2.2=0.5)
   /
  /
 /
/

- 真实世界的光线是 线性 的， 假设其大小为 x
- 进行光照计算(diffuse, specular，tone mapping)，filter，mipmap，抗锯齿时， 光线也要是线性的

            __
        /  
    /
 / 
|

- 存储在硬盘上的照片和视频(JPG,MPEG)是 凸起的 (y = x^0.5)


        |
        /
       /
    /
/
- 显示器会进行对光线做 凹操作 （y = x^2）


- 这样就可以直接将图片渲染在一般的 LCD 或 CRT上了 (x^0.5)^2 = x


```

# 2.实现方式
---

## 2.1 整体流程

以下是实现gamma correction的两套代码 code1 和 code2 的整体流程：

```
code 1
真实世界  存储在硬盘上(xx.jpg)    读取到shader中        在shader中先变换(^2)到线性空间
    x         ==>       x^0.5         ==>     x^0.5           ==>               x 
                                 tex*D(GL_RGB8)

在shader中进行光照计算          在shader中进行凸变换,输出到内存                   显示器凹变换(^2)，输出到真实世界    
        ==>         f(x)                 ==>                 ==> (f(x))^0.5           ==>                f(x)
                                  renderbuffer(GL_RGB)


```

```
code 2
真实世界  存储在硬盘上(xx.jpg)    读取到shader中
    x         ==>       x^0.5         ==>     x 
                                 tex*D(GL_SRGB8)

shader中进行光照计算                     输出到内存                             显示器凹变换(^2)，输出到真实世界    
        ==>         f(x)                 ==>                     (f(x))^0.5                ==>                f(x)
                                  renderbuffer(GL_SRGB)
```

## 2.2 具体代码

以下实现gamma correction的两套代码 code1 和 code2 的具体代码：

### 2.2.1 opengl纹理读取参考 （TODO）

```
code 1
//from hard disk to shader
GL_RGB8，x^0.5 => x^0.5
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB8, width, height, 0, GL_BGR, GL_UNSIGNED_BYTE, data);
```

```c++
code 2
//from hard disk to shader
GL_SRGB8, x^0.5 => x
glTexImage2D(GL_TEXTURE_2D, 0, GL_SRGB8, width, height, 0, GL_BGR, GL_UNSIGNED_BYTE, data); //?GL_SRGB_EXT 
```

### 2.2.2 opengl renderbuffer参考

```c++
code 1
定义成RGB

// fbo_width and fbo_height are the desired width and height of the FBO.
// For Opengl <= 4.4 or if the GL_ARB_texture_non_power_of_two extension
// is present, fbo_width and fbo_height can be values other than 2^n for
// some integer n.

// Build the texture that will serve as the color attachment for the framebuffer.
GLuint color_renderbuffer;
glGenRenderbuffers(1, &color_renderbuffer);
glBindRenderbuffer( GL_RENDERBUFFER, (GLuint)color_renderbuffer );
glRenderbufferStorage( GL_RENDERBUFFER, GL_RGBA8, fbo_width, fbo_height );
glBindRenderbuffer( GL_RENDERBUFFER, 0 );

// Build the texture that will serve as the depth attachment for the framebuffer.
GLuint depth_renderbuffer;
glGenRenderbuffers(1, &depth_renderbuffer);
glBindRenderbuffer( GL_RENDERBUFFER, (GLuint)depth_renderbuffer );
glRenderbufferStorage( GL_RENDERBUFFER, GL_DEPTH_COMPONENT, fbo_width, fbo_height );
glBindRenderbuffer( GL_RENDERBUFFER, 0 );

// Build the framebuffer.
GLuint framebuffer;
glGenFramebuffers(1, &framebuffer);
glBindFramebuffer(GL_FRAMEBUFFER, (GLuint)framebuffer);
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_RENDERBUFFER, color_renderbuffer);
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_ATTACHMENT, GL_RENDERBUFFER, depth_renderbuffer);

GLenum status = glCheckFramebufferStatus(GL_FRAMEBUFFER);
if (status != GL_FRAMEBUFFER_COMPLETE)
    // Error

glBindFramebuffer(GL_FRAMEBUFFER, 0);
```

```c++
code 2
定义成sRGB，便不用在shader中将最终光照计算出的linear的color进行^0.5的操作，硬件会自己对其进行^0.5的操作，再将其存储在内存中，之后再传送给显示器。
//TODO

glRenderbufferStorage( GL_RENDERBUFFER, GL_SRGBA8, fbo_width, fbo_height );
glEnable(GL_FRAMEBUFFER_SRGB);
```

### 2.2.3 shader参考

```c++
code 1
//if use GL_RGB8, color fetched from image is the original color value in image(x^0.5)
//glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB8, width, height, 0, GL_BGR, GL_UNSIGNED_BYTE, data);

//if use RGB for render buffer, color output to renderbuffer need to be ^1/2.2 outside shader

float specular = ...;
float3 color=pow(tex2D(samp,uv.xy),2.2);  //(x^0.5)^2=x
float diffuse = saturate(dot(N,L));
float3 finalColor = pow(color * diffuse + specular,1/2.2);
return finalColor;
```

```c++
code 2
//if use GL_SRGB8, color fetched from image has already been applied(x^0.5)^2 = x
//glTexImage2D(GL_TEXTURE_2D, 0, GL_SRGB8, width, height, 0, GL_BGR, GL_UNSIGNED_BYTE, data);

//if use sRGB for renderbuffer, color output to renderbuffer will be ^1/2.2 outside shader automatically, hence no need to do  ^1/2.2 inside shader

float specular = ...;
float3 color=tex2D(samp,uv.xy);  //x
float diffuse = saturate(dot(N,L));
float3 finalColor = color * diffuse + specular;
return finalColor;
```

# 3.一些概念

什么是gamma correction?

> Gamma correction is the practice of applying the inverse of the monitor transformation to the image pixels before they're displayed. That is, if we raise pixel values to the power 1/gamma before display, then the display implicitly raising to the power gamma will exactly cancel it out, resulting, overall, in a linear response.  --- GPU Gems[^1]

图片的mipmap应该在线性空间中生成
> If you are in a nonlinear color space with a gamma, say of 2.0, then that coarse-level texel with a value of 0.5 will be displayed at only 25 percent of the brightness. So you will see not-so-subtle errors in low-resolution mipmap levels, where the intensities simply aren't the correctly filtered results of the high-resolution levels.  --- GPU Gems[^1]


gamma correction仅限于rgb通道。

> Alpha channels, normal maps, displacement values (and so on) are almost certainly already linear and should not be corrected further, nor should any textures that you were careful to paint or capture in a linear fashion. --- GPU Gems[^1]


对于进入着色器的纹理，为了便于shader的书写，最简单的解决方案就是假定所有tex*D读取的纹理都已经在线性空间中(对于JPEG等X^2的，参考2.2.1 code2)。 这样便不再需要在shader中进行^2操作。

> Managing shaders that need to mix linear and nonlinear inputs can be an ugly logistical chore for the engine programmers and artists who provide textures. The simplest solution, in many cases, is to simply require all textures to be precorrected to linear space before they're delivered for use in rendering.  --- GPU Gems[^1]

对于multipass的渲染，应确保中间pass得到的也是线性的，确保最终的pass正确输出gamma校正的颜色给显示器。


# 4. 从头论述一下
---

人对于颜色的感知是非线性的，暗的时候 对颜色变化敏感， 亮的时候 对颜色变化不敏感。
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2015-11-17-eye-light-sensitivity.PNG)

因为图片越小越便于存储和传输，于是人们便想到可以用人眼的非线性特性对图像进行压缩： 暗的时候 多采样，亮的时候 少采样。 至于采样数的总的数量以及样本到底应该是怎么分布，应该是实验出来的。于是 本来是线性的光照信息，被按照这个曲线(gamma up)进行了压缩， **线性空间** 到 **gamma空间**， 然后被存储在每个通道8bit的图片上。 


如果不进行shader的光照计算，那么程序读取图片后，直接将其传递给framebuffer，直接传给CRT，CRT再apply一个gamma down曲线，光照信息从**gamma空间** 到**线性空间** 。


如果进行shader的光照计算，程序*读取图片*分为两种，分别将texture 设为 sRGB 和 RGB格式。 sRGB是硬件直接将图片会转换到**线性空间**；RGB格式的话，硬件不会自动进行gamm down转换操作，需要人工进行。 类似的，*framebuffer*也可以设置sRGB 和 RGB格式：sRGB是硬件直接将图片会转换到**线性空间**， 这意味着shader光照计算完成后，无需在结束shader前将结果进行gamma up；RGB格式的话，则需要进行gamma up操作。

注意 上述gamma correction的相关操作，只适用于8bit的图片(TIFF,JPEG,PNG,BMP,TGA)，对于HDR(float)的并不适用，HDR存储的是光照在线性空间的值。

其实本文讲的gamma correction的相关操作也可以称为 **线性工作流** (linear work flow)。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2015-11-17-lwf.PNG)

更多解释可以参考[^1][^2][^3][^4][^5][^6][^7]。


# Reference
---

[^1]: GPU Gems 3, Ch24 - The Importance of Being Linear [`LINK`](https://developer.nvidia.com/gpugems/GPUGems3/gpugems3_ch24.html)   
[^2]: Linear-Space Lighting [`EN`](http://filmicgames.com/archives/299) [`CN`](http://www.cnblogs.com/ghl_carmack/p/5401656.html)   
[^3]: Gamma Correction and Why It Matters [`LINK`](https://gamedevelopment.tutsplus.com/articles/gamma-correction-and-why-it-matters--gamedev-14466)    
[^4]: Gamma Correction in wikipedia [`EN`](https://en.wikipedia.org/wiki/Gamma_correction) [`CN`](https://zh.wikipedia.org/wiki/%E4%BC%BD%E7%91%AA%E6%A0%A1%E6%AD%A3)   
[^5]: Frequently Asked Questions about Gamma [`LINK`](http://www.poynton.com/PDFs/GammaFAQ.pdf) [`LINK`](https://pan.baidu.com/s/1dEDkzmD)  
[^6]: gamma的传说 [`LINK`](http://www.klayge.org/2011/02/26/gamma%E7%9A%84%E4%BC%A0%E8%AF%B4/)  
[^7]: 我理解的伽马校正 [`LINK`](http://blog.csdn.net/candycat1992/article/details/46228771)  
