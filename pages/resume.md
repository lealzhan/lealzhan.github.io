---
layout: page
title: 詹令
permalink: /resume/
show_meta: false
# imagefeature path is relative to images/ directory.
imagefeature: foo.png
published: true
description: "Resume lealzhan.github.io...."
category: views
comments: false
mathjax: false
noindex: false
sitemap:
    priority: 0.7
    changefreq: 'monthly'
    lastmod: 2019-9-24
# tags will be used as html meta keywords.    
tags:
  - "Ling Zhan"
  - “詹令”
  - “简历”
  - “resume”
---

## 联系方式

手机：(+86) 15060360529   Email：lealzhan@126.com
博客：[链接](https://lealzhan.github.io/lealzhan.github.io/)

## 工作经历


### [稿定（厦门）科技有限公司](https://www.gaoding.com/) (2018.6~)
**计算机图形学算法工程师** AI Labs 图形图像特效算法研发

- **3D字体特效** [项目网站](https://www.gaoding.com/topic/3791)    
	- 和设计人员紧密沟通，在短时间内独立完成**大部分算法**和**项目原型demo**研发，项目在较短时间内上线
		- 字体几何
			- 字体文件解析，镂空处理，正确处理一百多种中文字体
			- 路径采样算法，简单排版，字体路径光顺，字体膨胀与收缩，倒角，纹理坐标自动生成算法
		- 变形:二十多种变形样式 [链接](http://note.youdao.com/noteshare?id=b927fd528b398445a4d3f373703b720c)
		- 逐字变换:十几种变换样式 [链接](http://note.youdao.com/noteshare?id=5c4027f644b9b588c6942907485f2413)
		- 渲染调教:PBR，环境图预处理，[渐变材质](http://note.youdao.com/noteshare?id=f51e4d4d97c4dc1424573afdfae001a7)，描边，gamma矫正
		- 性能优化
		- 场景文件格式定义，场景保存和解析
		- 前端(JS)以及IOS SDK(C++)工程化
	- 申请两项专利（倒角生成算法以及 [渲染优化](http://note.youdao.com/noteshare?id=4379a54c94e6130d6a86999ffac5114d)）

- **轻量级基于OpenGL的三维引擎**
	- side project，独立完成	
	- 纹理，材质，光照，着色器，PBR，相机，场景图，关键帧动画，骨骼动画(Assimp读取FBX)，三维网格变形，粒子系统，和IMGUI的简单交互，读取obj三角网格，三维文字 

- **图像处理算法**   
	- 图像处理算法库(C语言)：多种模糊算法，泊松融合和金字塔融合，镜头光圈，渲染特效，色阶调整，透视变换，图层样式-内发光 等
	- 人像处理算法：PS中常用人像处理算法，人脸斑点自动检测和去除，肤色检测，IDW变形算法
	- 图像处理算法GLSL编程：搭建图像GLSL shader编程框架给其他组员使用

- **其他**    
	- 基于光线追踪的logo渲染研发
	- 基于变换反馈的高性能粒子系统 [链接](http://note.youdao.com/noteshare?id=983c47b30b5cfb6c8d5bc6731fe78594)
	- 带一名新人进行 图像变形算法 研发
	- 了解OpenCV,PhotoShop,three.js, OpenGL/WebGL

### [杭州图华科技有限公司](http://www.graphicchina.com/) (2017.2~2018.5)
**软件研发工程师** 研发部渲染组 高真实度的布料渲染引擎研发 [项目网站](http://www.frilly.com/)

- **负责改进光线追踪渲染管线**   
Ambient Occulision, HDR全景照片采集，软阴影，重要性采样，线性工作流，HDR色调映射, 纹理合成, 基于物理的渲染(PBR)，屏幕像素多采样

- **布料的体渲染算法**   
布料微观三维体数据的CT图像采集，处理和三维可视化；布料体渲染；基于真实照片的渲染参数优化拟合.[链接](http://note.youdao.com/noteshare?id=4a50fa60dbee04492109bf89f0ea52d2)

- **各向异性布料BRDF在环境光下的预处理渲染算法**   
算法研究，推演, 实现

- **其他**   
	- 独立从头搭建GPU光线追踪系统(side project) [链接](https://lealzhan.github.io/lealzhan.github.io/blog/2018/03/01/xyrender/)
	- 了解pbrt和Mitsuba开源光线追踪渲染器，Nvdia Optix SDK，Toolbag渲染器, 数值优化算法


### [福州网龙网络公司](http://www.nd.com.cn/) (2016.7~2016.11)
**研究员** 英魂之刃项目组 参与游戏地形编辑器的开发

改进烘焙功能； 颜色编辑（多层颜色控制，颜色高斯模糊）；地形的高度编辑（地形贴合三维曲线）

### [厦门幻世网络](http://www.avatarworks.com/) (2015.8~2016.7)
**3D引擎工程师** 引擎组 参与特效算法研发

- 在PC和移动端实现渲染特效算法（GLSL/ES）
	- 变色算法,基于物理的渲染(PBR),人体皮肤渲染,眼球渲染,头发渲染，布料渲染, 后期全屏特效
- 人体骨骼动画自动生成算法 [[GITHUB](https://github.com/lealzhan/reachAndGrasp)]

### [杭州图华科技有限公司](http://www.graphicchina.com/) (2014.11~2015.8)
**软件研发工程师** 研发部 模拟组 参与布料动力学引擎研发

[布料褶皱快速模拟](https://note.youdao.com/ynoteshare1/index.html?id=3149035beee8736b0a2f856c2091ac89&type=note), 刚体的运动模拟, 网格质量优化, 布料动力学模拟，GPU编程(cuda/thrust)


### [安赛乐米塔尔研发中心](http://corporate.arcelormittal.com/what-we-do/research-and-development/research-centres),东芝加哥市,美国(2013.5~2013.8,2014.4 ~ 2014.8)
**实习 研发工程师** 计算流体力学方向

多目标遗传算法优化设计喷嘴, 3D点云的曲面重构, 发表的论文[[论文1](http://pan.baidu.com/s/1o7C3elk)] [[论文2](http://pan.baidu.com/s/1pKoz8Kz)] [[论文3](http://pan.baidu.com/s/1c0XkUvm)]



## 教育经历
### 卡内基梅隆大学,匹兹堡,美国(2012.8~2013.12)
**硕士**, 机械工程学, GPA:3.88/4.0
课程: 计算机图形学,JAVA J2EE编程,工程计算,计算生物仿真及可视化,复杂流体力学,有限元分析,计算机辅助设计  
实验室: [Computational Bio-Modeling Lab](http://jessicaz.me.cmu.edu/) [[论文](http://www.tandfonline.com/doi/abs/10.1080/21681163.2013.776270)]

### 哈尔滨工业大学(2008.8~2012.7)
**本科**, 工程力学, 航天学院, GPA：85/100

## 其他

### 技能清单
- 编程语言： C/C++, Python, MATLAB, JavaScript
- 熟悉 计算机图形学 和 计算机视觉算法
- 比较了解 OpenGL/ES/WebGL, GLSL/ES, CUDA, OptiX, OpenCV
- 了解的三维引擎 three.js/Ogre/Urho3D

### 杂项
- 2012,	TOEFL：93 GRE：800+730+3.5
- 2012,	美国数学建模竞赛	二等奖
- 2010,	全国大学生基础力学竞赛	校一等奖 国家三等奖