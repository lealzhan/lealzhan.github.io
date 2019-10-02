---
layout: page
title: Ling Zhan
permalink: /resume-en/
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

## Personal Information

 - Name: Ling Zhan 詹令
 - Degree: M.S. in Mechanical Engineering, Carnegie Mellon University（Graduate: 2013.12）
 - 5 years work experience in Computer Graphics
 - Contact: Mobile：+86 15060360529  Email：lealzhan@126.com Linkedin：[LINK](http://cn.linkedin.com/in/lingzhan) Blog：[LINK](https://lealzhan.github.io/lealzhan.github.io/archive/)

## Work Experience

### [Gaoding](https://www.gaoding.com/), Xiamen, China (2018.6~)
**Computer Graphics Algorithm Engineer** AI Labs, Visual Effects Team

- **3D Text** [LINK](https://www.gaoding.com/topic/3791)    
	- Worked closely with designers to build the project prototype
	- Led the algorithm development, including: text parser, geometry processing, HDR/PBR rendering, deformation, transformation, performance optimization
	- Implemented core algorithm for multi-platforms(web/IOS)
	- Applied for two patents: 3d text bevel generation; a rendering method for 3d text side wall [LINK](http://note.youdao.com/noteshare?id=4379a54c94e6130d6a86999ffac5114d)

- **A Lightweight 3D Engine based on OpenGL**
	- features: material, texture, light, shader, PBR, camera, scene graph, keyframe animation, skeleton animation(loaded with assimp), mesh deformation, particle system, UI with [ImGui](https://github.com/ocornut/imgui), 3D text, shadow mapping

- **Image Processing**   
	- Implemented image processing algorithms with C language: mutiple blur algorithms, poisson and pyrimid blending, lens flare, light rendering effects, perspective transformation
	- Portrait processing: automatically human face defect detection and elimination, skin detection, IDW deformation
	- Implemented image processing algorithms using GLSL, GLSL ES: Surface Blur, IDW deformation, etc
	- Developed a heterogeneous image processing pipeline with [BGFX](https://github.com/bkaradzic/bgfx) and an in-house CPU image processing library
	
- **Misc**    
	- Particle system based on transform feedback [LINK](http://note.youdao.com/noteshare?id=983c47b30b5cfb6c8d5bc6731fe78594)
	- Learned OpenCV,PhotoShop,three.js,OpenGL/WebGL,3D Engine(BGFX,Urho3D/Filament)

### [Graphicchina](http://www.graphicchina.com/), Hanzhou, China (2017.2~2018.5)
**Computer Graphics Algorithm Engineer** R&D Department, Rendering Team

- **Real-time Ray Tracing Rendering Engine for Cloth** [LINK](https://www.frilly.com/product)
	- Ray tracing pipeline   
	ambient occulision, HDR environment map capturing，soft shadow，importance sampling，linear workflow，HDR tone mapping, texture synthesis, PBR，reconstruction filter
	- Prefilter environment map for anisotropic cloth BRDF

- **Volumetric Rendering for Cloth** [LINK](http://note.youdao.com/noteshare?id=4a50fa60dbee04492109bf89f0ea52d2)  
Micro-CT scanning, processing, and visualizaiton for cloth; volumetric rendering; rendering parameters acquiring 

- **A Lightweight Ray Tracing Engine using NVIDIA OptiX** [LINK](https://lealzhan.github.io/lealzhan.github.io/blog/2018/03/01/xyrender/)
	- Tried to implement a ray tracer like [Mitsuba](http://www.mitsuba-renderer.org/), but much more efficient with the power of GPU
	- Features：emitter(point light,area light,HDR env map), BSDF(lambert, microcylinder),integrator(Whitted), camera, geometry, material, saving and loading scene json file

### [NetDragon Websoft Inc](http://www.nd.com.cn/)., Fuzhou, China(2016.7~2016.11) ##
**Game Engine Researcher**

- Developed new features for **game terrain editor** based on in-house game engine (C++, Lua, xml).
- Developed an automatically-delta-update tool for **game assets packer** based on Jenkins.

### [Avatar Works](http://www.avatarworks.com/), Xiamen, China (2015.8 ~ 2016.7)
**3D Engine Engineer**

- Programmed shaders for pc and mobile platforms: physically based rendering, real-time sss skin rendering,  eye rendering, full screen effect, cloth rendering.
- Customized particle system for special effects.
- Developed tools for artists.
- Developed an example-based human motion animation algorithm for reach and grasp（Skeleton Animation，k-Nearest Neighbor，Inverse Kinematic，Motion Interpolation， Discrete Collision Detection）[[GITHUB](https://github.com/lealzhan/reachAndGrasp)]


### [Graphicchina](http://www.graphicchina.com/), Hanzhou, China (2014.11 ~ 2015.8)
**Software R&D Engineer** R&D Department, Physical Simulation Team

- Was one of the key members to develop the state-of-the-art cloth simulation system. 
- Implemented Cloth Simulation Algorithms, including
	- Wrinkle Meshes
	- Least-Squares Rigid Motion Using SVD 
	- Triangular Mesh Optimization
- Learned numerical method, parallel programming(CUDA, thrust, SSE/AVX), collision detection,  cloth simulation algorithm

### [ArcelorMittal R&D Center](http://corporate.arcelormittal.com/what-we-do/research-and-development/research-centres)，East Chicago，USA（2013.5 ~ 2013.8， 2014.4 ~ 2014.8）
**R&D Engineer | Intern**

- Applied Computer Fluid Dynamics and Digital Geometry Processing in the analysis and improvement of steel-making process.
- Published three papers [[PAPER1](http://pan.baidu.com/s/1o7C3elk)] [[PAPER2](http://pan.baidu.com/s/1pKoz8Kz)] [[PAPER3](http://pan.baidu.com/s/1c0XkUvm)]

### [Computational Bio-Modeling Lab](http://jessicaz.me.cmu.edu/) at Carnegie Mellon University，Pittsburgh, USA（2012.08~2013.5, 2013.09~2013.12）
**Graduate Research Assistant**

- Developed quad-dominant mesh generation algorithms based on [harmonic function](https://lealzhan.github.io/lealzhan.github.io/blog/2013/04/30/Quad-Harmonic/) and [bubble dynamics](https://lealzhan.github.io/lealzhan.github.io/blog/2013/10/20/Quad-Bubble/).
- Conduct fluid-structure interaction simulation for cerebral aneurysms using ANSYS. Co-authored one Paper [[PAPER](http://www.tandfonline.com/doi/abs/10.1080/21681163.2013.776270)]


## Education

### Carnegie Mellon University, Pittsburgh, PA, USA（2012.8 ~ 2013.12）
M.S. in Mechanical Engineering，GPA:3.88/4.0

Coursework: Computer Graphics, Mechanics of Complex Fluids, Computer Aided Design, Finite Element Method, Engineering Computation, JAVA J2EE Programming, Computational Bio-Modeling and Visualization

### Harbin Institute of Technology, Harbin, China (2008.8 ~ 2012.7）
B.E. in Aerospace Engineering and Mechanics，GPA：85/100

### National Cheng Kung University，Tainan, Taiwan, China（2010.9 ~ 2011.1）
Exchange Student in Mechanical Engineering，GPA：3.77/4.0


### Skills

- C/C++, MATLAB，Python, Java, Javascript
- Visual Studio, Xcode，SVN/Git
- Computer Graphics, Digital Geometry Processing,Numerical Methods,Numerical Optimization
- Parallel Programming（OpenMP，CUDA，thrust，SSE/AVX）
- OpenGL, GLSL, GLSLES

### Misc
- 2012，	TOEFL：93 GRE：800+630+3.5
- 2012，	U.S. Interdisciplinary Contest in Modeling, Honorable Mention


