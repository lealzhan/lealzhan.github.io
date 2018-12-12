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

## three.js
three.js是JavaScript编写的WebGL第三方库。


## Render the Scene with Camera
三个重要的概念 Renderer, Scene 和 Camera.

``` javascript
var scene = new THREE.Scene();
var camera = new THREE.PerspectiveCamera( 75, window.innerWidth/window.innerHeight, 0.1, 1000 );
var renderer = new THREE.WebGLRenderer();
renderer.setSize( window.innerWidth, window.innerHeight );
document.body.appendChild( renderer.domElement );
```

### Camera 相机

两种相机类型

#### 透视相机 

https://threejs.org/docs/#api/en/cameras/PerspectiveCamera

THREE.PerspectiveCamera(fov, aspect, near, far) 

- fov 			可视角度 
- aspect ratio 	实际窗口的纵横比（宽/高）
- near 			近处的裁面的距离
- far 			远处的裁面的距离

这些参数定义了透视相机的**视锥体 Viewing frustum**
 
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-1.jpg)



#### 正交相机 

https://threejs.org/docs/#api/en/cameras/OrthographicCamera

Three.OrthographicCamera(left,right,top,bottom,near,far)

- left 			左平面与相机中心点的垂直距离 
- right 		右平面与相机中心点的垂直距离 
- top 			上平面与相机中心点的垂直距离 
- bottom 		下平面与相机中心点的垂直距离 
- near 			近平面与相机中心点的垂直距离 
- far 			远平面与相机中心点的垂直距离 


![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-2.jpg)

#### 全景相机 StereoCamera

https://threejs.org/docs/#api/en/cameras/StereoCamera


其他相机参数

camera.position.z = 5;

## Code
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

## Reference

https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene
http://www.cnblogs.com/xulei1992/p/5703232.html