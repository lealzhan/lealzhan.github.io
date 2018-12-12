---
layout: post
title:  "一个简单的three.js"
date:   2016-2-1 21:23:21 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - opengl
  - webgl
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
2016.2.1       


# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# three.js
three.js是JavaScript编写的WebGL第三方库。


# Render the Scene with Camera

``` javascript
    renderer.render( scene, camera );
``` 

三个重要的概念 Renderer, Scene 和 Camera.

``` javascript
var scene = new THREE.Scene();
var camera = new THREE.PerspectiveCamera( 75, window.innerWidth/window.innerHeight, 0.1, 1000 );
var renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight );
document.body.appendChild( renderer.domElement );
```

## Camera 相机

三种相机类型

### 透视相机 [[DOC](https://threejs.org/docs/#api/en/cameras/PerspectiveCamera)]

THREE.PerspectiveCamera(fov, aspect, near, far) 

- fov 			可视角度 
- aspect ratio 	实际窗口的纵横比（宽/高）
- near 			近处的裁面的距离
- far 			远处的裁面的距离

这些参数定义了透视相机的**视锥体 Viewing frustum**
 
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-1.jpg)



### 正交相机 

[[DOC](https://threejs.org/docs/#api/en/cameras/OrthographicCamera)]

Three.OrthographicCamera(left,right,top,bottom,near,far)

- left 			左平面与相机中心点的垂直距离 
- right 		右平面与相机中心点的垂直距离 
- top 			上平面与相机中心点的垂直距离 
- bottom 		下平面与相机中心点的垂直距离 
- near 			近平面与相机中心点的垂直距离 
- far 			远平面与相机中心点的垂直距离 


![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-2.jpg)

### 全景相机 StereoCamera

[[DOC](https://threejs.org/docs/#api/en/cameras/StereoCamera)]


其他相机参数

camera.position.z = 5;

## Renderer 渲染器

### WebGLRenderer

[[DOC](https://threejs.org/docs/#api/en/renderers/WebGLRenderer)]

**.domElement**
    
A canvas where the renderer draws its output.
This is automatically created by the renderer in the constructor (if not provided already); you just need to add it to your page like so:

     document.body.appendChild( renderer.domElement );


**.setSize**

Resizes the output canvas to (width, height) with device pixel ratio taken into account, and also sets the viewport to fit that size, starting in (0, 0). Setting updateStyle to false prevents any style changes to the output canvas.



## Scene 场景


### Mesh

    var cube = new THREE.Mesh( geometry, material );

[[DOC](https://threejs.org/docs/index.html#api/en/objects/Mesh)]

#### BufferGeometry

An **efficient** representation of mesh, line, or point geometry.

[[DOC](https://threejs.org/docs/index.html#api/en/core/BufferGeometry)]

``` javascript
var geometry = new THREE.BufferGeometry();
// create a simple square shape. We duplicate the top left and bottom right
// vertices because each vertex needs to appear once per triangle.
var vertices = new Float32Array( [
	-1.0, -1.0,  1.0,
	 1.0, -1.0,  1.0,
	 1.0,  1.0,  1.0,

	 1.0,  1.0,  1.0,
	-1.0,  1.0,  1.0,
	-1.0, -1.0,  1.0
] );

// itemSize = 3 because there are 3 values (components) per vertex
geometry.addAttribute( 'position', new THREE.BufferAttribute( vertices, 3 ) );
var material = new THREE.MeshBasicMaterial( { color: 0xff0000 } );
var mesh = new THREE.Mesh( geometry, material );

```


#### Geometry

    var geometry = new THREE.BoxGeometry( 1, 1, 1 );

[[DOC](https://threejs.org/docs/index.html#api/en/core/Geometry)]

a **less efficient** but **easier-to-use** representation of geometry

``` javascript
var geometry = new THREE.Geometry();

geometry.vertices.push(
	new THREE.Vector3( -10,  10, 0 ),
	new THREE.Vector3( -10, -10, 0 ),
	new THREE.Vector3(  10, -10, 0 )
);

geometry.faces.push( new THREE.Face3( 0, 1, 2 ) );

geometry.computeBoundingSphere();

```

#### Material

	var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );

[[DOC](https://threejs.org/docs/index.html#api/en/materials/Material)]


##### MeshBasicMaterial

[[DOC](https://threejs.org/docs/index.html#api/en/materials/MeshBasicMaterial)]

##### MeshLambertMaterial

[[DOC](https://threejs.org/docs/index.html#api/en/materials/MeshLambertMaterial)]



##### MeshPhongMaterial

[[DOC](https://threejs.org/docs/index.html#api/en/materials/MeshPhongMaterial)]

The material uses a non-physically based Blinn-Phong model for calculating reflectance.

##### MeshStandardMaterial

[[DOC](https://threejs.org/docs/index.html#api/en/materials/MeshStandardMaterial)]


A standard physically based material, using Metallic-Roughness workflow.
标准的PBR渲染模型，使用 Metallic-Roughness 工作流。


##### MeshPhysicalMaterial

[[DOC](https://threejs.org/docs/index.html#api/en/materials/MeshPhysicalMaterial)]

An extension of the MeshStandardMaterial that allows for greater control over reflectivity. Note that for best results you should always specify an environment map when using this material.

??






# Code
将以下代码保存为html格式的文件，就可以运行了。   

``` javascript
<html>
	<head>
		<title>My first Three.js app</title>
		<style>
			body { margin: 0; }
			canvas { width: 100%; height: 100% }
		</style>
	</head>
	<body>
<script src="http://threejs.org/build/three.min.js"></script>
		<script>
			var scene = new THREE.Scene();
			var camera = new THREE.PerspectiveCamera( 75, window.innerWidth/window.innerHeight, 0.1, 1000 );

			var renderer = new THREE.WebGLRenderer();
			renderer.setSize( window.innerWidth, window.innerHeight );
			document.body.appendChild( renderer.domElement );

			var geometry = new THREE.BoxGeometry( 1, 1, 1 );
			var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
			var cube = new THREE.Mesh( geometry, material );
			scene.add( cube );

			camera.position.z = 5;

			var render = function () {
				requestAnimationFrame( render );

				cube.rotation.x += 0.1;
				cube.rotation.y += 0.1;

				renderer.render(scene, camera);
			};

			render();
		</script>
	</body>
</html>

```


![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-0.png)

# Reference

https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene
http://www.cnblogs.com/xulei1992/p/5703232.html