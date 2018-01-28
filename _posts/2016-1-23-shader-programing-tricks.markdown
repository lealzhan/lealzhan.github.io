---
layout: post
title:  "Shader编程的几个tricks"
date:   2016-1-23 20:47:38 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2016.1.23   


# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

写了快半年的shader了，记录一些比较技巧(偏门)的东西。

# 1. shader里的世界坐标系

```
        Z
       ∧
       /
 x<---●
      |
      V
      y
```

上图是说 往屏幕里边是Z正方向，左边是x正方向，下方是y正方向。这是在ogre 1.9 搭建的渲染环境下的glsl，glsles，其他环境下没有测试过。   
这样的话，就可以在shader里直接给定light方向的vector，实现 固定的方向光源。 比如，light_vector_0 = vec3(0, 0, 1)，就可以看到光从屏幕往里照射了。


# 2. 跨平台： 对glsl和glsles通用的shader

glsl和glsles大部分是一样的，所以可以对不一样的部分定义一个宏来快速切换。以下代码在glsl中可以运行，把第一行启用的话，就可以在glsles中使用了。 

```c++
// #define GL_ES  //uncomment this line for GL_ES
#if defined(GL_ES)
    #version 100
    #extension GL_EXT_shader_texture_lod : require

    precision highp int;
    precision highp float;
    #define TEXTURE_2D_LOD texture2DLodEXT   
    #define TEXTURE_CUBE_LOD textureCubeLodEXT   

#else
    #version 120
    #extension GL_ARB_shader_texture_lod : require
    #define TEXTURE_2D_LOD texture2DLod
    #define TEXTURE_CUBE_LOD textureCubeLod
#endif

//接着写shader主体

```

这只是一个非常初级的着色器跨平台解决方案，应该有比较高级的做法, 待研究。


# 3. 坐标系转换

坐标系转换是个大坑，不过还是有一些规律可以稍微记一下。

一般情况下，法线贴图涉及的坐标转换是把世界空间下的光照和视线向量转换到切线空间中，用的就是以下代码的rotation矩阵。

如果想要把切线空间的法向量转换到世界空间中（基于物理的渲染读取textureCube会用到世界坐标系），只要将rotation做一个转置，然后乘以法向量，就可以了。


```c++
 //inside fragment shader
 /////////////---------- transfer N from tangent space to world space  ----------/////////////
    vec3 v_WS_normal_normalized = normalize(v_WS_normal);
    vec3 v_WS_tangent_normalized = normalize(v_WS_tangent);
    //v_WS_tangent_normalized = normalize(v_WS_tangent_normalized - v_WS_normal_normalized * v_WS_tangent_normalized * v_WS_normal_normalized);
    //WS->TS rotation
    vec3 binormal = normalize(cross(v_WS_normal_normalized, v_WS_tangent_normalized));
    mat3 rotation = mat3(vec3(v_WS_tangent_normalized[0], binormal[0], v_WS_normal_normalized[0]),  //first coloum
                         vec3(v_WS_tangent_normalized[1], binormal[1], v_WS_normal_normalized[1]),  //second coloum
                         vec3(v_WS_tangent_normalized[2], binormal[2], v_WS_normal_normalized[2])); //third coloum    
    //TS->WS inv_rotation
    mat3 inv_rotation = transpose(rotation);
    vec3 WS_N = normalize(inv_rotation * (TS_N));        //TS --> WS
    /////////////---------- transfer N from tangent space to world space END  ----------///////////// 
```

此外在做preintegrate skin shading时，如果在fragment shader里实时计算curvature，那么也涉及到了比较复杂的空间坐标转换。（一般不会实时计算curvature，可以用xnormal预计算curvature并储存成贴图）

```c++
    //Curvature  // can be optimized by reading curvatures from texture or ogre
    vec3 v_WS_normal_normalized = normalize(v_WS_normal);
    vec3 v_WS_tangent_normalized = normalize(v_WS_tangent);
    v_WS_tangent_normalized = normalize(v_WS_tangent_normalized - v_WS_normal_normalized * v_WS_tangent_normalized * v_WS_normal_normalized);
    //WS->TS matrix rotation
    vec3 binormal = cross(v_WS_normal_normalized, v_WS_tangent_normalized);
    mat3 rotation = mat3(vec3(v_WS_tangent_normalized[0], binormal[0], v_WS_normal_normalized[0]),
                         vec3(v_WS_tangent_normalized[1], binormal[1], v_WS_normal_normalized[1]),
                         vec3(v_WS_tangent_normalized[2], binormal[2], v_WS_normal_normalized[2]));
    //TS->WS matrix inv rotation
    mat3 inv_rotation = transpose(rotation);
    vec3 WS_N = inv_rotation * texture2D(normalMap, v_texCoord, u_normal_map_blur_level).xyz;        //TS --> WS
    vec3 WS_P = v_WS_vertex.xyz;//(world*v_vertex).xyz;    //v_WS_vertex.xyz;         //TS --> WS

    float deltaWSNormal = saturate(length( fwidth(normalize(WS_N)) )); 
    float deltaWSPosition =        length( fwidth ( ((WS_P) ) ));
    float curvature = deltaWSNormal / deltaWSPosition * u_CurvatureScale;
```


# 4. 纹理读取系列函数的 lod （level of detail）

如果你的程序根本不想精确使用纹理的mipmap，请忽略。
以下适用于opengles 2.0 和 opengl 1.2 在OGRE下的使用情况。 其他情况不一定适用。

先说说最终的解决方案。如果想跨opengles 2.0 和 opengl 1.2，想在fragment shader里精确控制你想读取哪一层mipmap，那么可以在fragment shader的顶部定义这样一个宏。然后通过TEXTURE_2D_LOD 和TEXTURE_CUBE_LOD读取精确纹理的某一个mipmap。

```c++
// #define GL_ES
#if defined(GL_ES)
    #version 100
    #extension GL_EXT_shader_texture_lod : require

    precision highp int;
    precision highp float;
    #define TEXTURE_2D_LOD texture2DLodEXT   
    #define TEXTURE_CUBE_LOD textureCubeLodEXT   

#else
    #version 120
    #extension GL_ARB_shader_texture_lod : require
    #define TEXTURE_2D_LOD texture2DLod
    #define TEXTURE_CUBE_LOD textureCubeLod
#endif

//接着写shader主体
```


不管是对gl还是gles来说，fragment shader里是没有texture2DLod这个函数，所以只能通过扩展函数来调用。并且gl和gles的扩展是不一样的。


#### 4. 附录0

```c++
glsles, fragment shader                                     mipmaped                       not mipmaped
texture2D(text,coord)                               系统计算的lod                              0
texture2D(text,coord,bias)                   bias+系统计算的lod             
texture2DLod(text,coord,lod)
GL_EXT_shader_texture_lod 扩展：
texture2DLodEXT(text,coord,lod)                lod                                         

glsl, fragment shader                                     mipmaped                           not mipmaped
texture2D(text,coord)                               系统计算的lod                              0
texture2D(text,coord,bias)                   bias+系统计算的lod             
texture2DLod(text,coord,lod)
GL_ARB_shader_texture_lod 扩展：
texture2DLod(text,coord,lod)                       lod                                         
```

* mipmaped，not mipmaped是指系统读取的纹理是否含有多层mipmap。
* cube和2D的情况一样，不再重复。

#### 4. 附录1

如果ogre配置有问题，导致只能读取cube texture的第一层mipmap，那么可以手工实现一个读取纹理lod功能。原理就是读取第0, 3，7层纹理，然后根据输入的lod在相邻的两层纹理间线性插值。效果还不错。

```c++
//note: this func is designed for cubemap with 128*128(8mipmaps)
uniform samplerCube env0;
uniform samplerCube env1;
uniform samplerCube env2;
vec3 textureCubeLodLing( vec3 R, float Mip)
{ 
    vec3 temp = vec3(1.0);
    if(Mip<3.0 || Mip == 3.0){
        float weight = Mip/3.0;
         temp = (1.0 - weight) * TEXTURE_CUBE_LOD( env0, R, 0.0).rgb +  weight * TEXTURE_CUBE_LOD( env1, R, 0.0).rgb ;
     }
    else if(Mip>3.0){
        float weight = abs(Mip - 3.0)/4.0;
         temp = (1.0 - weight) * TEXTURE_CUBE_LOD( env1, R, 0.0).rgb +  weight * TEXTURE_CUBE_LOD( env2, R, 0.0).rgb ;
     }
    return temp;
}
```




# 5. 善于利用宏

除了前边提到的跨平台的宏定义之外，还可以在shader中定义

```c++
//#define DEBUG
#if defined(DEBUG)
   测试函数
#endif
```

来进行一些测试功能。


