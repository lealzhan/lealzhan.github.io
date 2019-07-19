---
layout: post
title:  "OpenGL and Engine"
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
- glut：遗弃   
- freeglut: glut的替代版   
- GLFW      
- Qt  

# [OpenGL Loading Library](https://www.khronos.org/opengl/wiki/OpenGL_Loading_Library)
用于加载(包含)不同平台的opengl头文件 opengl.h。

- GLEW   
- GL3W
- GLAD
   


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


