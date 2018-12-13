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
last modified 2018.12.12

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# three.js
three.js是JavaScript编写的WebGL第三方库。 本文主要是对official doc进行结构化。


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

### [透视相机](https://threejs.org/docs/#api/en/cameras/PerspectiveCamera)

THREE.PerspectiveCamera(fov, aspect, near, far) 

- fov 			可视角度 
- aspect ratio 	实际窗口的纵横比（宽/高）
- near 			近处的裁面的距离
- far 			远处的裁面的距离

这些参数定义了透视相机的**视锥体 Viewing frustum**
 
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-1.jpg)



### [正交相机 ](https://threejs.org/docs/#api/en/cameras/OrthographicCamera)

Three.OrthographicCamera(left,right,top,bottom,near,far)

- left 			左平面与相机中心点的垂直距离 
- right 		右平面与相机中心点的垂直距离 
- top 			上平面与相机中心点的垂直距离 
- bottom 		下平面与相机中心点的垂直距离 
- near 			近平面与相机中心点的垂直距离 
- far 			远平面与相机中心点的垂直距离 


![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-2-1-threejs-2.jpg)

### [全景相机 StereoCamera](https://threejs.org/docs/#api/en/cameras/StereoCamera)


其他相机参数

camera.position.z = 5;

## Renderer 渲染器

### [WebGLRenderer](https://threejs.org/docs/#api/en/renderers/WebGLRenderer)

**.domElement**
    
A canvas where the renderer draws its output.
This is automatically created by the renderer in the constructor (if not provided already); you just need to add it to your page like so:

     document.body.appendChild( renderer.domElement );


**.setSize**

Resizes the output canvas to (width, height) with device pixel ratio taken into account, and also sets the viewport to fit that size, starting in (0, 0). Setting updateStyle to false prevents any style changes to the output canvas.



## [Scene 场景](https://threejs.org/docs/index.html#api/en/scenes/Scene) 

### [Group](https://threejs.org/docs/index.html#api/en/objects/Group)

``` javascript
//create a group and add the two cubes
//These cubes can now be rotated / scaled etc as a group
var group = new THREE.Group();
group.add( cubeA );
group.add( cubeB );

scene.add( group );
```

### [LOD](https://threejs.org/docs/index.html#api/en/objects/LOD)

``` javascript
var lod = new THREE.LOD();

//Create spheres with 3 levels of detail and create new LOD levels for them
for( var i = 0; i < 3; i++ ) {

	var geometry = new THREE.IcosahedronBufferGeometry( 10, 3 - i )

	var mesh = new THREE.Mesh( geometry, material );

	lod.addLevel( mesh, i * 75 );

}

scene.add( lod );
```

### [Lights](https://threejs.org/docs/index.html#api/en/lights/Light)

#### [AmbientLight](https://threejs.org/docs/index.html#api/en/lights/AmbientLight)

``` javascript
var light = new THREE.AmbientLight( 0x404040 ); // soft white light
scene.add( light );
```

#### [DirectionalLight](https://threejs.org/docs/index.html#api/en/lights/DirectionalLight)

``` javascript
// White directional light at half intensity shining from the top.
var directionalLight = new THREE.DirectionalLight( 0xffffff, 0.5 );
scene.add( directionalLight );
```


### [Line](https://threejs.org/docs/#api/en/objects/Points)

A continuous line.

``` javascript
var material = new THREE.LineBasicMaterial({
	color: 0x0000ff
});

var geometry = new THREE.Geometry();
geometry.vertices.push(
	new THREE.Vector3( -10, 0, 0 ),
	new THREE.Vector3( 0, 10, 0 ),
	new THREE.Vector3( 10, 0, 0 )
);

var line = new THREE.Line( geometry, material );
scene.add( line );
```


### [Points](https://threejs.org/docs/#api/en/objects/Points)

### [Sprite](https://threejs.org/docs/#api/en/objects/Sprite)

A sprite is a plane that always faces towards the camera, generally with a partially transparent texture applied.

``` javascript
var spriteMap = new THREE.TextureLoader().load( "sprite.png" );
var spriteMaterial = new THREE.SpriteMaterial( { map: spriteMap, color: 0xffffff } );
var sprite = new THREE.Sprite( spriteMaterial );
scene.add( sprite );
```

### [Mesh](https://threejs.org/docs/index.html#api/en/objects/Mesh)

var cube = new THREE.Mesh( geometry, material );

#### [BufferGeometry](https://threejs.org/docs/index.html#api/en/core/BufferGeometry)

An **efficient** representation of mesh, line, or point geometry.

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

##### [ExtrudeBufferGeometry](https://threejs.org/docs/index.html#api/en/geometries/ExtrudeBufferGeometry)

``` javascript
var length = 12, width = 8;

var shape = new THREE.Shape();
shape.moveTo( 0,0 );
shape.lineTo( 0, width );
shape.lineTo( length, width );
shape.lineTo( length, 0 );
shape.lineTo( 0, 0 );

var extrudeSettings = {
	steps: 2,
	depth: 16,
	bevelEnabled: true,
	bevelThickness: 1,
	bevelSize: 1,
	bevelSegments: 1
};

var geometry = new THREE.ExtrudeBufferGeometry( shape, extrudeSettings );
var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
var mesh = new THREE.Mesh( geometry, material ) ;
scene.add( mesh );
```

##### [TextBufferGeometry](https://threejs.org/docs/index.html#api/en/geometries/TextBufferGeometry)

``` javascript
var loader = new THREE.FontLoader();

loader.load( 'fonts/helvetiker_regular.typeface.json', function ( font ) {

	var geometry = new THREE.TextBufferGeometry( 'Hello three.js!', {
		font: font,
		size: 80,
		height: 5,
		curveSegments: 12,
		bevelEnabled: true,
		bevelThickness: 10,
		bevelSize: 8,
		bevelSegments: 5
	} );
} );
```


#### [Geometry](https://threejs.org/docs/index.html#api/en/core/Geometry)

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


##### [ExtrudeGeometry](https://threejs.org/docs/index.html#api/en/geometries/ExtrudeGeometry)

``` javascript
ar length = 12, width = 8;

var shape = new THREE.Shape();
shape.moveTo( 0,0 );
shape.lineTo( 0, width );
shape.lineTo( length, width );
shape.lineTo( length, 0 );
shape.lineTo( 0, 0 );

var extrudeSettings = {
	steps: 2,
	depth: 16,
	bevelEnabled: true,
	bevelThickness: 1,
	bevelSize: 1,
	bevelSegments: 1
};

var geometry = new THREE.ExtrudeGeometry( shape, extrudeSettings );
var material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
var mesh = new THREE.Mesh( geometry, material ) ;
scene.add( mesh );
```

##### [TextGeometry](https://threejs.org/docs/index.html#api/en/geometries/TextGeometry)

``` javascript
var loader = new THREE.FontLoader();

loader.load( 'fonts/helvetiker_regular.typeface.json', function ( font ) {

	var geometry = new THREE.TextGeometry( 'Hello three.js!', {
		font: font,
		size: 80,
		height: 5,
		curveSegments: 12,
		bevelEnabled: true,
		bevelThickness: 10,
		bevelSize: 8,
		bevelSegments: 5
	} );
} );
```



#### [Material](https://threejs.org/docs/index.html#api/en/materials/Material)


##### [MeshBasicMaterial](https://threejs.org/docs/index.html#api/en/materials/MeshBasicMaterial)

##### [MeshLambertMaterial](https://threejs.org/docs/index.html#api/en/materials/MeshLambertMaterial)



##### [MeshPhongMaterial](https://threejs.org/docs/index.html#api/en/materials/MeshPhongMaterial)

The material uses a non-physically based Blinn-Phong model for calculating reflectance.

##### [MeshStandardMaterial](https://threejs.org/docs/index.html#api/en/materials/MeshStandardMaterial)


A standard physically based material, using Metallic-Roughness workflow.
标准的PBR渲染模型，使用 Metallic-Roughness 工作流。


##### [MeshPhysicalMaterial](https://threejs.org/docs/index.html#api/en/materials/MeshPhysicalMaterial)

An extension of the MeshStandardMaterial that allows for greater control over reflectivity. Note that for best results you should always specify an environment map when using this material.

??

##### [ShaderMaterial](https://threejs.org/docs/index.html#api/en/materials/ShaderMaterial)

A material rendered with custom shaders. 

重要。需要专门研究。


``` javascript
var material = new THREE.ShaderMaterial( {

	uniforms: {

		time: { value: 1.0 },
		resolution: { value: new THREE.Vector2() }

	},

	vertexShader: document.getElementById( 'vertexShader' ).textContent,

	fragmentShader: document.getElementById( 'fragmentShader' ).textContent

} );

```

# 更新框架

??

``` javascript
var camera, scene, renderer;
init();
animate();

function init( ) 
{
	...
}

function onWindowResize()
{
	camera.aspect = window.innerWidth / window.innerHeight;
	camera.updateProjectionMatrix();
	renderer.setSize( window.innerWidth, window.innerHeight );
}

function animate() 
{
	requestAnimationFrame( animate );
	render();
}

function render() 
{
	renderer.render( scene, camera );
}

```



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