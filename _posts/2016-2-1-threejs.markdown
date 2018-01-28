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

## three.js
three.js是JavaScript编写的WebGL第三方库。   

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

