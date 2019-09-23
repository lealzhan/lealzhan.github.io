---
layout: post
title:  "实时渲染：OpenGL and Engine"
date:   2018-12-5 17:12:32 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - opengl
  - webgl
  - three.js
  - 3d
  - modeling
  - physics
  - rendering
  - game engine
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
2018-12-5    

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

本文旨在梳理 关于OpenGL的一大堆让人眼花缭乱的库，渲染引擎，和游戏引擎。

# Graphics API
- OpenGL   
- OpenGL ES   
- WebGL   
- Metal   
- Vulkan   
- Direct3D   

# Shading Language
- OpenGL: glsl   
- OpenGLes/WebGL: glsl es   
- Direct3D: hlsl   
- NVDIA : Cg   
- Metal: Metal Shading Language/MSL


# OpenGL 上下文及窗口管理
EGL : OpenGLES@Android，Web?   
WGL : OpenGL DX? && Windows?   
GLX: OpenGL && linux   
EAGL: OpenGLES@IOS, Metal?   
NSGL: Metal && OSX   
ppapi: nacl   

# OpenGL 上下文和窗口管理 的跨平台库
- SDL: 交叉编译SDL来提供OpenGL上下文和窗口管理的跨平台支持。相比各个平台原生OpenGL上下文和窗口管理的API，SDL灵活性会低一些。
	- **URHO3D**使用SDL来管理跨平台图形环境上下文。具体见Urho3D::Graphics::SetMode(). Urho3D中，有Graphics作为基类，实现了OGLGraphics，D3D9Graphics，D3D11Graphics,分别用于管理opengl/opengles,  d3d9,d3d11的图形上下文。
- glut：遗弃   
- freeglut: glut的替代版   
- GLFW      
- Qt  

# [OpenGL Loading Library](https://www.khronos.org/opengl/wiki/OpenGL_Loading_Library)
用于加载(包含)不同平台的opengl头文件 opengl.h。

- **GLEW** ：PC端的opengl及其扩展文件加载
- **GL3W**
- **GLAD**

Urho3D中加载opengl及其扩展是如下定义的：
``` c++
// urho3d
#if defined(ANDROID) || defined (RPI) || defined (__EMSCRIPTEN__)
#include <GLES2/gl2.h>
#include <GLES2/gl2ext.h>
#elif defined(IOS)
#include <OpenGLES/ES2/gl.h>
#include <OpenGLES/ES2/glext.h>
#else
#include <GLEW/glew.h>
#endif
```   


# 渲染引擎（光栅化）(渲染引擎是游戏引擎的一部分)
## BGFX 
- [GITHUB](https://github.com/bkaradzic/bgfx)
- 主要是一个人在维护，开源，免费，对各个平台opengl api进行封装的纯粹的渲染引擎，有一套类glsl的跨平台shader语言，
- 实际案例一般，没有中文文档, 不包含粒子系统等其他游戏引擎的常见系统; 虽然支持跨平台，但是跨平台支持不太友好，实际使用起来需要一定量的开发. 
- 需要依赖bx和bimg两个额外的库。作者没有发布版本的概念，很难切换到合适的版本(bgfx,bimg,bx)在各个平台同时可用。
		 

## OGRE 3D 
- [HOMEPAGE](https://www.ogre3d.org/)
- 全称 Object-Oriented Graphics Rendering Engine，引擎是基于面向对象编程构建的， 历史很悠久的渲染引擎。
- 开源免费，1.7+ MIT许可，跨平台支持，有中文文档，社区支持比较好，除渲染系统外还包含动画系统和粒子系统，支持常见商用编辑器导入场景模型。
- 商业案例有限。

## OSG 
- [HOMEPAGE](http://www.openscenegraph.org/)  [GITHUB](https://github.com/openscenegraph/OpenSceneGraph)
- The Open Scene Graph
- GPLP, 社区维护， 中文文档, 比较流行，粒子系统等可以通过别人开发的插件支持，跨平台

## Filament
- [GITHUB](https://github.com/google/filament)


## The Forge 
- [LINK](https://github.com/ConfettiFX/The-Forge)
- 比较小众.

## gkEngine
- [LINK](https://github.com/gameknife/gkEngine)
- 小众

## pixi.js 
- [HOMEPAGE](http://pixijs.com/) [GITHUB](https://github.com/pixijs/pixi.js)
- webgl渲染引擎,2D,很流行,很多商业案例。无编辑器。

## G3D 
- [HOMEPAGE](https://alibaba.github.io/G3D/)[GITHUB](https://github.com/alibaba/G3D) [INFO](http://taobaofed.org/blog/2018/03/05/intro-to-g3d/)
- 小型webgl渲染引擎，3d, 淘宝前端2018.3开源，

## LiteScene 
- [GITHUB](https://github.com/jagenjo/litescene.js) [编辑器webglstudio.js]()
- 开源 MIT许可, 3d webgl引擎, 1.4mb, 主要是一个人在维护， 2013至今
- 在webglstudio设计场景，导出的场景文件可以用LiteScene跑起来（目前感觉可能有些效果跑不全）

## webglstudio.js 
- [HOMEPAGE](https://webglstudio.org/) [GITHUB](https://github.com/jagenjo/webglstudio.js) [EDITOR](https://webglstudio.org/demo/)
- 基于LiteScene的3d webgl编辑器，开源 MIT许可, 主要是一个人在维护，2015年至今
- 比较正统的3d场景编辑器的写法，层次结构化的场景节点，可以往节点挂各种component，可扩展性强。
- 系统复杂，全部用js写，可能需要较长时间上手。因为不是商业的引擎，可能会有比较多的bug，维护的可能不是很好。

## sketch.js 
- [GITHUB](https://github.com/soulwire/sketch.js/) [DEMO](http://soulwire.github.io/sketch.js/)
- 2d，可以和three.js混合调用[demo](http://soulwire.github.io/sketch.js/examples/three.html)

## PlayCanvas 
- [HOMEPAGE](https://playcanvas.com/) [文档](https://developer.playcanvas.com/zh/)
- 开源 MITLicence 有编辑器， 有较多成功的商业案例，国内不太火。18年被snapchat收购.
- 引擎本身开源。编辑器不开源，可以在线免费使用编辑器开发3D交互应用，但是代码会被开源。代码想闭源的话，要收费，有点类似3d交互用应用的github模式了。

## Skia


# 游戏引擎 （一般游戏引擎会有自己单独研发的渲染引擎）
## Unity3D 
- [HOMEPAGE](https://unity3d.com/cn?_ga=2.81811670.1139994982.1544154612-1333628586.1544154612) 
- 商业公司维护、个人免费、全球最流行的3d/2d游戏引擎 、js/c# 开发脚本、 编辑器支持 跨平台发布 详细的中文文档，视觉效果一流，商业案例非常多，人员好招聘。
- 商业838元每月，不开源，不能确定进行二次开发的程度（比如能不能对粒子系统进行修改，而不仅仅是用脚本调用api）

## Unreal 
- [HOMEPAGE](https://www.unrealengine.com/zh-CN/what-is-unreal-engine-4)
- 视觉效果非常好，可以拿来做CG电影。 
- 流程化，很专业，需要一个团队来开发，
- 不好学，门槛高，（免费使用，在发行时支付 5%）

## Urho3D 
- [HOMEPAGE](https://urho3d.github.io/) [GITHUB](https://github.com/urho3d/Urho3D)
- 历史比较悠久(2008~), 一般流行，开源，免费，MIT许可， 文档一般，代码可读性较好，有场景编辑器，angelscript脚本语言，跨平台，
- 渲染效果一般，商业案例少。无中文文档， 编辑器是用angelscript编写的，很难进行二次开发。

## cocos2d-x 
- [HOMEPAGE](https://www.cocos.com/) [DOC](https://www.cocos.com/docs/native/v2/getting-started/architecture-and-directory-structure/zh.html) [GITHUB](https://github.com/cocos2d/cocos2d-x/)
- MIT许可，免费开源，2d引擎，触控科技支持，工具链齐全，商用案例多，文档齐全
- 不支持3D

## godot  
- [HOMEPAGE](https://godotengine.org/) [GITHUB](https://github.com/godotengine/godot) [doc CN](https://docs.godotengine.org/zh_CN/latest/) 
- 优点： 免费 开源 MIT许可 开源社区驱动 编辑器支持 跨平台支持 详细的中文文档 2D&3D支持 
- 缺点： 案例 效果

## three.js  
- [HOMEPAGE](https://threejs.org/) [GITHUB](https://github.com/mrdoob/three.js/) [EDITOR](https://threejs.org/editor/)
- 很流行，3D。简单的编辑器。支持多种外部编辑器的场景文件 maya 3dmax blender。 


# 三维引擎选定考虑要素

- 2D与3D
两个都需要支持。

- 功能特性
不仅仅是3D游戏引擎常见的 渲染系统，粒子系统，图像传统后处理系统（shader），动画/视频系统，物理模拟系统，几何系统，输入输出， 还会涉及到 AI图像处理。

- 性能
偏向于实时。如果是高质量的结果，一定时间的处理也可以接受？

- 引擎的渲染画质
引擎的渲染效果好，渲染时间短。

- 编程语言
偏向于 c/c++。Js也可以接受。

- 工作流
最好有成熟好用的，并且容易二次开发的编辑器，方便美术人员进行场景素材创作 以及 技术人员进行二次功能扩张开发。
常见的编辑器： 场景编辑、模型编辑、动画编辑、粒子编辑
第三方插件，如3ds max、maya的导出插件。

- 文档和学习资料
越多越好。

- 平台支持
网页端，移动端android和ios，mac，windows pc最好都支持

- 可维护性和可扩展性
代码开源，可读性强，容易修改。

- 成熟性
比较流行，使用范围广，历史悠久。很多现成资源(代码，素材)可以收集，减少开发时间。人员比较好招聘。

- 收费情况
偏向于免费，收费不高的也可以接受。

- 产品落地
内部素材生成独立工具/应用？一个sdk？


# 其他

## webglstudio.js 在线场景编辑器

- demo
	+ 粒子系统 + 路径动画
	+ 后处理特效：像素化
	+ 后处理特效：透镜弯曲 + 地板纹理
	+ 抗锯齿

- 使用 litescene.js 运行由wgbglstudio导出的场景文件

- iphone 8plus的chrome也可以跑，但是因为精度问题，深度测试不对。

- 还不知道怎么运行动画.
(已解决，需要在root添加playanimation component)


## three.js 引擎详细评估
three.js [官方demo](https://threejs.org/examples/)

### three.js 代码算法保密性问题

#### 介绍
前端代码是无法加密的

#### 可能的解决方案
- 部分算法写成wasm封装起来，这样算法就不大可能被公开。
相对c/c++代码进行混淆，在封装成很wasm。待验证。

- 写成后端服务的形式
响应速度可能会比较慢

- 代码混淆


#### 参考

- 前端如何给 JavaScript 加密（不是混淆）？ [LINK](https://www.zhihu.com/question/47047191)
- wasm的反编译 [LINK](https://www.colabug.com/4425538.html)
- 如何在WebGL开发中保护美术资源（以及为什么你不需要保护它们） [LINK](http://www.hiwebgl.com/?p=792)


### three.js 对三维字体的支持

#### 默认提供的控制参数 [DEMO]
- font 
提供的字体helvetiker， optimer，gentilis，droid sans，droid serif
可用opentype.js来扩展字体库
- size — Float。字体大小，默认值为100
- height — Float。挤出文本的厚度。默认值为50。
- curveSegments — Integer。（表示文本的）曲线上点的数量。默认值为12。
- bevelEnabled — Boolean。是否开启斜角，默认为false。
- bevelThickness — Float。文本上斜角的深度，默认值为20。
- bevelSize — Float。斜角与原始文本轮廓之间的延伸距离。默认值为8。
- bevelSegments — Integer。斜角的分段数。默认值为3。
- bold

#### 一些改进点
- 响应速度不理想
- 解决font需重复加载的问题
- 渲染效果还有很大提升空间
- 深挖各种渲染特效
- 支持中文
- 转换后的字体文件大(楷体 55Mb)需优化

### three.js 布料动力学 
[DEMO](https://threejs.org/examples/#webgl_animation_cloth)   
[CODE](https://github.com/mrdoob/three.js/blob/master/examples/webgl_animation_cloth.html)    
[NOTE](https://lealzhan.github.io/lealzhan.github.io/blog/2018/12/13/threejs-cloth/)   

### three.js 场景导入 loader

[NOTE](http://note.youdao.com/noteshare?id=7de14ec07fd6083f69ed5599b00f1be1)

#### 初步完成glTF模型导入three.js测试
- 可以导入网格，材质(PBR)，纹理。
- 无法导入光源
- 无法导入后期全屏特效