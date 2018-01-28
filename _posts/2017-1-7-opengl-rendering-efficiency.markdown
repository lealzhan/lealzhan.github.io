---
layout: post
title:  "如何在OpenGL中进行高效渲染"
date:   2017-1-7 10:47:38 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - computer graphics
  - opengl
  - rendering
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
2017.1.7   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# 1. 几种绘制方式的比较
---
<br />

#### **最原始的画图函数 glBegin**

glBegin，glEnd。 CPU调用opengl次数多，并且每次绘制时都要将数据从CPU传递到GPU。 因而是效率最低的方式。

> **1.在glBegin()和glEnd()之间可调用的函数**  
>函数        函数意义  
>glVertex*() 设置顶点坐标  
>glColor*() 设置当前颜色  
>glIndex*() 设置当前颜色表  
>glNormal*() 设置法向坐标  
>glEvalCoord*() 产生坐标  
>glCallList(),glCallLists() 执行显示列表  
>glTexCoord*() 设置纹理坐标  
>glEdgeFlag*() 控制边界绘制  
>glMaterial*() 设置材质  
> **2.几何图元类型和说明**  
>类型 说明  
>GL_POINTS 单个顶点集  
>GL_LINES 多组双顶点线段  
>GL_POLYGON 单个简单填充凸多边形  
>GL_TRIANGLES 多组独立填充三角形  
>GL_QUADS 多组独立填充四边形  
>GL_LINE_STRIP 不闭合折线  
>GL_LINE_LOOP 闭合折线  
>GL_TRIANGLE_STRIP 线型连续填充三角形串  
>GL_TRIANGLE_FAN 扇形连续填充三角形串  
>GL_QUAD_STRIP 连续填充四边形串  
> -- [glBegin百度百科](http://baike.baidu.com/link?url=B1oGkMRVRFgvBXobgYCloEvPRD9nEQ5D79un8rXwb9fHW54WeVf1Cnty5w6SxPe6wQLxLjsD6NlwbsfAITeAFK)


```c++
//con: have to send all vertex data from CPU to GPU every frame, which can be extremely slow if vertex number is huge.
//refer to cg lec2 slide

glBegin(GL_TRIANGLES);
		//GL_TRIANGLES告诉gl每隔三个顶点截取数据
		glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
		glVertex2f(-0.5, 0.5);

		glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
		glVertex2f(0.5, 0.5);

		glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
		glVertex2f(0.5, -0.5);

		//------------------------------------------------
		glColor3f(1.0f, 0.0f, 0.0f); // sets color to blue
		glVertex2f(-0.7, -0.7);

		glColor3f(1.0f, 0.0f, 0.0f); // sets color to blue
		glVertex2f(-0.5, -0.5);

		glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
		glVertex2f(-0.5, 0.5);

	glEnd();
```
<br />

#### **显示列表 Display List**：

glCallList首先将三角形数据从CPU内存传递到GPU内存，三角形数据不能改变（不会再次将三角形数据从CPU内存传递到GPU内存）。glCallList绘制三角形时，**不进行内存传输，直接调用GPU内存**，所以效率很高。
显示列表的缺点正在于它的古板： **绘制的三角形数据一旦设定，就不容许更改**，所以它只适合对一些“固定”的东西的绘制进行包装。

```c++
//only need to fransfer mem from cpu to gpu once --> very fast,  gpu mem consumming, only for static object rendering
//refer to cg lec2 slide

int list0 = -1;

void InitDisplayListGLTriangle()
{
	list0 = glGenLists(1);
	glNewList(list0, GL_COMPILE);
		glBegin(GL_TRIANGLES);
		    //GL_TRIANGLES告诉gl每隔三个顶点截取数据
			glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
			glVertex2f(-0.5, 0.5);

			glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
			glVertex2f(0.5, 0.5);

			glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
			glVertex2f(0.5, -0.5);

			//------------------------------------------------
			glColor3f(1.0f, 0.0f, 0.0f); // sets color to blue
			glVertex2f(-0.7, -0.7);

			glColor3f(1.0f, 0.0f, 0.0f); // sets color to blue
			glVertex2f(-0.5, -0.5);

			glColor3f(0.0f, 0.0f, 1.0f); // sets color to blue
			glVertex2f(-0.5, 0.5);

		glEnd();
	glEndList();
}

void DrawDisplayCallbackGLTriangle()
{
	if (list0 == -1)
		InitDisplayListGLTriangle(); //transfer buffer from cpu mem to gpu mem

	glCallList(list0);  //directly draw, no need to transfer buffer from cpu mem to gpu mem
}

```
<br />

#### **顶点数组 VA Vertex Array**：
先调用**glEnableClientState**(GL_VERTEX_ARRAY，GL_COLOR_ARRAY，GL_NORMAL_ARRAY)，再调用**glVertexPointer，glColorPointer，glNormalPointer**设置所要绘制的三角形数据在**CPU内存**上的指针（索引，坐标，颜色，法线）。
在客户端设置顶点数组，**每次**执行**glDrawArray / glDrawElement**时, 程序会把三角形数据从**CPU内存传递到GPU内存**。


```c++
//con: have to send all vertex data from CPU to GPU every frame, which can be extremely slow if vertex number is huge.
//refer to cg lec2 slide

//cg p1 的思路
void DrawVertexArray_glVertexPointer()
{
	int num_vertices = 3;

//0. vertex pointer
	int num_of_value_per_vertex = 2;
	float vertices[] = {1.0f, 0.0f,// 0.0f,// 1.0f,
						0.0f, 1.0f,// 0.0f,// 1.0f,
						0.0f, 0.0f//, 1.0f//, 0.0f
	};

	//int num_of_value_per_vertex = 3;
	//float vertices[] = {1.0f, 0.0f, 0.0f,// 1.0f,
	//					0.0f, 1.0f, 0.0f,// 1.0f,
	//					0.0f, 0.0f, 1.0f//, 0.0f
	//};

	/*int num_of_value_per_vertex = 4;
	float vertices[] = { 1.0f, 0.0f, 0.0f, 1.0f,
						 0.0f, 1.0f, 0.0f, 1.0f,
						 0.0f, 0.0f, 1.0f, 0.0f
	};*/

	glEnableClientState(GL_VERTEX_ARRAY);
	glVertexPointer(num_of_value_per_vertex, GL_FLOAT, sizeof(float)* num_of_value_per_vertex, vertices); //             size: numb of vertices.  stride: bytes per vertex

//1. color pointer
	int num_of_color_channel_per_vertex = 3;
	float colors[] = { 1.0f, 0.0f, 0.0f, 
					   0.0f, 1.0f, 0.0f,
		               0.0f, 0.0f, 1.0f
	};
	/*int num_of_color_channel_per_vertex = 4;
	float colors[] = { 1.0f, 0.0f, 0.0f, 0.0f,
		               0.0f, 1.0f, 0.0f, 0.0f,
		               0.0f, 0.0f, 1.0f, 0.0f
	};*/
	glEnableClientState(GL_COLOR_ARRAY);
	glColorPointer(num_of_color_channel_per_vertex, GL_FLOAT, sizeof(float)* num_of_color_channel_per_vertex, colors);

//2. index pointer
	unsigned int indices[] = {0, 1, 2};

////3. normal
//	float normals[] = { 1.0f, 0.0f, 0.0f,
//						0.0f, 1.0f, 0.0f,
//						0.0f, 0.0f, 1.0f
//	};
//	glEnableClientState(GL_NORMAL_ARRAY);
//	glNormalPointer(GL_FLOAT, 0, normals);

//final. draw
	// transfer buffer data from cpu to gpu
	glDrawElements(GL_TRIANGLES, 1*3, GL_UNSIGNED_INT, indices);
	
	glDisableClientState(GL_VERTEX_ARRAY);
	glDisableClientState(GL_COLOR_ARRAY);
	glDisableClientState(GL_NORMAL_ARRAY);
```
<br />

#### **顶点缓存对象 VBO Vertex Buffer Object**：

预先将三角形数据从CPU内存传递到GPU内存，并且可以指定三角形数据改变的情况（GL_STATIC_DRAW，GL_STREAM_DRAW，GL_DYNAMIC_DRAW），这样OpenGL便可以自动优化CPU到GPU的内存拷贝行为。然后，每次绘制时，先bind buffer，再设定顶点数据，然后执行glDrawElement。

顶点缓存比顶点数组好的地方就是，在调用glDrawElement时，OpenGL不会每次调用glDrawElements时都将三角形数据从CPU拷贝到GPU，而是基于glBufferData的参数（GL_STATIC_DRAW，GL_STREAM_DRAW，GL_DYNAMIC_DRAW）以及实际情况来拷贝数据。 这样就可以大大降低三角形数据从CPU拷贝到GPU带来的时间损失。


```  c++
//vbo 使用 drawElement时，不再像vertex array（DrawVertexArray_glVertexPointer()）那样每次都从cpu传递数据到gpu，而是基于buffer的usage参数（GL_STATIC_DRAW GL_STREAM_DRAW GL_DYNAMIC_DRAW）来决定是否将数据从cpu传输到gpu。

// GL_STATIC_DRAW，GL_STREAM_DRAW，GL_DYNAMIC_DRAW 用于给OpenGL系统提醒：预期数据是一直不变、数据每帧变一次或几帧变一次、数据每帧变两三次以上，方便硬件内部优化吧。 //http://www.zwqxin.com/archives/opengl/learn-vbo.html
//STREAM: The data store contents will be modified once and used at most a few times.
//STATIC: The data store contents will be modified once and used many times.
//DYNAMIC: The data store contents will be modified repeatedly and used many times.
//refer to https://www.opengl.org/sdk/docs/man2/xhtml/glBufferData.xml
//refer to https://www.opengl.org/wiki/Vertex_Specification#Vertex_Buffer_Object
// http://www.cnblogs.com/iRidescent-ZONE/p/5475337.html
//part0： 假定data是static的，GL_STATIC_DRAW

bool if_vbo_init = false;
GLuint vertId, idxId, colorId;
// Some method called during initialization
void initialize_triangle()
{
	GLfloat vertices[] = { 0.0, 0.0, 
						   0.0, 1,
						   1, 1 
	};
	GLfloat colors[] = { 1.0, 0.0, 0.0, 0.0,
						 0.0, 1.0, 0.0, 0.0, 
						 0.0, 0.0, 1.0, 1.0
	};
	GLuint indices[] = { 0, 1, 2 };
	
	glGenBuffers(1, &vertId); //1 means generate 1 buffer                                       // Create buffers
	glBindBuffer(GL_ARRAY_BUFFER, vertId);                                                      // Bind buffers to modify/render them
	glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW); //GL_STREAM_DRAW // Load data into buffers(GPU)
	glBindBuffer(GL_ARRAY_BUFFER, 0);

	glGenBuffers(1, &colorId);
	glBindBuffer(GL_ARRAY_BUFFER, colorId);
	glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //GL_STREAM_DRAW
	glBindBuffer(GL_ARRAY_BUFFER, 0);

	glGenBuffers(1, &idxId); 
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId); 
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

	if_vbo_init = true;
}
// 第0种使用vbo的方法：固定渲染管线
void DrawVboDisplayCallback0(){
	if (!if_vbo_init)
		initialize_triangle();
	
	glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableClientState(GL_VERTEX_ARRAY);
		glVertexPointer(2, GL_FLOAT, 0, 0);
	glBindBuffer(GL_ARRAY_BUFFER, 0);

	glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glEnableClientState(GL_COLOR_ARRAY);
		glColorPointer(4, GL_FLOAT, 0, 0);
	glBindBuffer(GL_ARRAY_BUFFER, 0);

	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glDrawElements(GL_TRIANGLES, 1*3, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

	glDisableClientState(GL_VERTEX_ARRAY);
	glDisableClientState(GL_COLOR_ARRAY);
}

// 第1种使用vbo的方法L：可编程渲染管线
//note: default setting in OpenGL, vertrex attribute index of pos, normal, color is 0, 2, 3
#define VERTEX_POS_INDX 0
#define VERTEX_NORMAL_INDX 2
#define VERTEX_COLOR_INDX 3

void DrawVboDisplayCallback1(){
	if (!if_vbo_init)
		initialize_triangle();

	glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableVertexAttribArray(VERTEX_POS_INDX); // 固定管线使用glEnableClientState(GL_VERTEX_ARRAY)
		glVertexAttribPointer(VERTEX_POS_INDX, 2, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glVertexPointer(4, GL_FLOAT, 0, vertexes);
	glBindBuffer(GL_ARRAY_BUFFER, 0);

	glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glEnableVertexAttribArray(VERTEX_COLOR_INDX); // 固定管线使用glEnableClientState(GL_COLOR_ARRAY)
		glVertexAttribPointer(VERTEX_COLOR_INDX, 4, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glColorPointer(4, GL_FLOAT, 0, colores);
	glBindBuffer(GL_ARRAY_BUFFER, 0);

	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glDrawElements(GL_TRIANGLES, 1 * 3, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

	glDisableVertexAttribArray(VERTEX_POS_INDX);
	glDisableVertexAttribArray(VERTEX_COLOR_INDX);
}
```
<br />

#### **顶点数组对象 VAO Vertex Array Object**：

VAO类似于call list，可以将多个VBO的命令封装在里边。大大减少了CPU下对opengl的调用，因而提高了效率。

>VBO是Vertex Buffer Object, VAO是Vertex Array Object。 VAO是OpenGL 3.0以后才引入的新东西，但是在2.0版本中做为扩展接口。
>
>VBO其实就是显卡中的显存，为了提高渲染速度，可以将要绘制的顶点数据缓存在显存中，这样就不需要将要绘制的顶点数据重复从CPU发送到GPU, 浪费带宽资源。
>
>而VAO则是一个容器，可以包括多个VBO,  它类似于以前的call list, 由于它进一步将VBO容于其中，所以绘制效率将在VBO的基础上更进一步。
>
>--- [http://www.oschina.net/question/565065_64379](http://www.oschina.net/question/565065_64379)

```  c++
//代码非本人原创，侵删
#define _USE_MATH_DEFINES
#include <math.h>
//#define BUFFER_OFFSET(offset) ((GLvoid*) NULL + offset)
#define BUFFER_OFFSET(offset) offset
#define NumberOf(array) (sizeof(array)/sizeof(array[0]))

typedef struct {
	GLfloat x, y, z;
} vec3;
typedef struct {
	GLfloat x, y, z, w;
} vec4;

typedef struct {
	vec3 xlate; /* Translation */
	GLfloat angle;
	vec3 axis;
} XForm;
enum { Cube, Cone, NumVAOs };
GLuint VAO[NumVAOs];
GLenum PrimType[NumVAOs];
GLsizei NumElements[NumVAOs];
XForm Xform[NumVAOs] = {
	{ { -2.0, 0.0, 0.0 }, 0.0, { 0.0, 1.0, 0.0 } },
	{ { 0.0, 0.0, 2.0 }, 0.0, { 1.0, 0.0, 0.0 } }
};
GLfloat Angle = 0.0;
bool if_vao_init = false;
void initVAO()
{
	enum { Vertices, Colors, Elements, NumVBOs };
	GLuint buffers[NumVBOs];
	glGenVertexArrays(NumVAOs, VAO);

	glBindVertexArray(VAO[Cube]);
	{
		GLfloat cubeVerts[][3] = {
			{ -1.0, -1.0, -1.0 },
			{ -1.0, -1.0, 1.0 },
			{ -1.0, 1.0, -1.0 },
			{ -1.0, 1.0, 1.0 },
			{ 1.0, -1.0, -1.0 },
			{ 1.0, -1.0, 1.0 },
			{ 1.0, 1.0, -1.0 },
			{ 1.0, 1.0, 1.0 },
		};
		GLfloat cubeColors[][3] = {
			{ 0.0, 0.0, 0.0 },
			{ 0.0, 0.0, 1.0 },
			{ 0.0, 1.0, 0.0 },
			{ 0.0, 1.0, 1.0 },
			{ 1.0, 0.0, 0.0 },
			{ 1.0, 0.0, 1.0 },
			{ 1.0, 1.0, 0.0 },
			{ 1.0, 1.0, 1.0 },
		};
		GLubyte cubeIndices[] = {
			0, 1, 3, 2,
			4, 6, 7, 5,
			2, 3, 7, 6,
			0, 4, 5, 1,
			0, 2, 6, 4,
			1, 5, 7, 3
		};
		
		glGenBuffers(NumVBOs, buffers); //should be binded in vertex_array?
		glBindBuffer(GL_ARRAY_BUFFER, buffers[Vertices]);
		glBufferData(GL_ARRAY_BUFFER, sizeof(cubeVerts), cubeVerts, GL_STATIC_DRAW); //should be binded in vertex_array?
		glVertexPointer(3, GL_FLOAT, 0, BUFFER_OFFSET(0));
		glEnableClientState(GL_VERTEX_ARRAY);

		glBindBuffer(GL_ARRAY_BUFFER, buffers[Colors]);
		glBufferData(GL_ARRAY_BUFFER, sizeof(cubeColors), cubeColors, GL_STATIC_DRAW);
		glColorPointer(3, GL_FLOAT, 0, BUFFER_OFFSET(0));
		glEnableClientState(GL_COLOR_ARRAY);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, buffers[Elements]);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(cubeIndices), cubeIndices, GL_STATIC_DRAW);
		PrimType[Cube] = GL_QUADS;
		NumElements[Cube] = NumberOf(cubeIndices);
	}

	glBindVertexArray(VAO[Cone]);
	{
		int i, idx;
		float dTheta;
		#define NumConePoints 36  
		/* We add one more vertex for the cone's apex */
		GLfloat coneVerts[NumConePoints + 1][3] = {
			{ 0.0, 0.0, 1.0 }
		};
		GLfloat coneColors[NumConePoints + 1][3] = {
			{ 1.0, 1.0, 1.0 }
		};
		GLubyte coneIndices[NumConePoints + 1];
		dTheta = 2 * M_PI / (NumConePoints - 1);
		idx = 1;
		for (i = 0; i < NumConePoints; ++i, ++idx) {
			float theta = i*dTheta;
			coneVerts[idx][0] = cos(theta);
			coneVerts[idx][1] = sin(theta);
			coneVerts[idx][2] = 0.0;
			coneColors[idx][0] = cos(theta);
			coneColors[idx][1] = sin(theta);
			coneColors[idx][2] = 0.0;
			coneIndices[idx] = idx;
		}

		glGenBuffers(NumVBOs, buffers);
		glBindBuffer(GL_ARRAY_BUFFER, buffers[Vertices]);
		glBufferData(GL_ARRAY_BUFFER, sizeof(coneVerts), coneVerts, GL_STATIC_DRAW);
		glVertexPointer(3, GL_FLOAT, 0, BUFFER_OFFSET(0));
		glEnableClientState(GL_VERTEX_ARRAY);

		glBindBuffer(GL_ARRAY_BUFFER, buffers[Colors]);
		glBufferData(GL_ARRAY_BUFFER, sizeof(coneColors), coneColors, GL_STATIC_DRAW);
		glColorPointer(3, GL_FLOAT, 0, BUFFER_OFFSET(0));
		glEnableClientState(GL_COLOR_ARRAY);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, buffers[Elements]);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(coneIndices), coneIndices, GL_STATIC_DRAW);
		PrimType[Cone] = GL_TRIANGLE_FAN;
		NumElements[Cone] = NumberOf(coneIndices);
	}
	if_vao_init = true;
}
void DrawVAOdisplay()
{
	if (!if_vao_init)
		initVAO();
	int i;
	
	for (i = 0; i < NumVAOs; ++i) {
		
		glBindVertexArray(VAO[i]);
		glDrawElements(PrimType[i], NumElements[i], GL_UNSIGNED_BYTE, BUFFER_OFFSET(0));
	}
}

```

<br />

#### **小结**

总的来说， 内存使用效率 glBegin < VA < VBO。  
VAO和callList都是为了减少使用cpu进行OpenGL调用而存在。两者具体的区别有待研究。  

# 2. glDrawArrays, glDrawElements,glMultiDrawElements, glDrawElementsInstanced 间的区别
---
<br />

glDrawArrays, glDrawElements,glMultiDrawElements, glDrawElementsInstanced 间的区别

<br />

#### **glDrawArrays**
glDrawArrays指定顶点数据（Position，color，normal，texCoord）， OpenGL会根据 mode 自动进行图元索引。 

mode有：GL_POINTS、GL_LINES、GL_LINE_LOOP、GL_LINE_STRIP、GL_TRIANGLES、GL_TRIANGLE_STRIP、GL_TRIANGLE_FAN。

常用的mode有：

GL_TRIANGLES：　　　　　　　　0-1-2　　　3-4-5　　　6-7-8　　　T = [n-2 n-1 n]

GL_TRIANGLE_STRIP：　　　0-1-2　　　2-1-3　　　　2-3-4　　　n is odd : T = [n-1 n-2 n]  n is even : T = [n-2 n-1 n]

GL_TRIANGLE_FAN：　　　　　0-1-2　　　0-2-3　　　　0-3-4　　　T = [0 n-1 n]

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-1.jpg)

    更多关于mode的描述： opengl super bible 5th edition， ch3.4 connecting the dots

以下对于glDrawElements的升级版本glMultiDrawElements，glDrawElementsInstanced，都有相应的glDrawArrays的版本（glMultiDrawArrays， glDrawArraysInstanced）。

```c++
namespace drawarrays{
	bool if_vbo_init_DrawArrays = false;
	GLuint vertId, colorId;
	// Some method called during initialization
	void initialize_triangle_DrawArrays()
	{
		GLfloat vertices[] = { 0.0, 0.0,
							   0.0, 1.0,
							   1.0, 1.0,
							   0.0, 0.0,
							   1.0, 1.0,
							   1.0, 0.0
		};
		GLfloat colors[] = { 1.0, 0.0, 0.0, 1.0,
							 0.0, 1.0, 0.0, 1.0,
							 0.0, 0.0, 1.0, 1.0,
							 1.0, 0.0, 0.0, 1.0,
							 0.0, 0.0, 1.0, 1.0,
							 1.0, 1.0, 1.0, 1.0
		};
		//GLuint indices[] = { 0, 1, 2 };

		glGenBuffers(1, &vertId); //1 means generate 1 buffer                                       // Create buffers
		glBindBuffer(GL_ARRAY_BUFFER, vertId);                                                      // Bind buffers to modify/render them
		glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW); //GL_STREAM_DRAW // Load data into buffers(GPU)
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &colorId);
		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //GL_STREAM_DRAW
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		/*glGenBuffers(1, &idxId);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);*/

		if_vbo_init_DrawArrays = true;
	}


	// 第1种使用vbo的方法：可编程渲染管线
	//note: default setting in OpenGL, vertrex attribute index of pos, normal, color is 0, 2, 3
#define VERTEX_POS_INDX 0
#define VERTEX_NORMAL_INDX 2
#define VERTEX_COLOR_INDX 3

	void DrawVboDisplayCallbackDrawArrays(){
		if (!if_vbo_init_DrawArrays)
			initialize_triangle_DrawArrays();

		glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableVertexAttribArray(VERTEX_POS_INDX); // 固定管线使用glEnableClientState(GL_VERTEX_ARRAY)
		glVertexAttribPointer(VERTEX_POS_INDX, 2, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glVertexPointer(4, GL_FLOAT, 0, vertexes);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glEnableVertexAttribArray(VERTEX_COLOR_INDX); // 固定管线使用glEnableClientState(GL_COLOR_ARRAY)
		glVertexAttribPointer(VERTEX_COLOR_INDX, 4, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glColorPointer(4, GL_FLOAT, 0, colores);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glDrawArrays(GL_TRIANGLES, 0, 2 * 3); //1*3 = numb_tri * 3
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		glDisableVertexAttribArray(VERTEX_POS_INDX);
		glDisableVertexAttribArray(VERTEX_COLOR_INDX);
	}
}
```
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-2.PNG)


<br />

#### **glDrawElements**
glDrawElements除了指定顶点数据（Position，color，normal，texCoord）外，和glDrawArray相比最大的差别是 还需要指定图元的**index**索引数据。


```  c++
namespace drawelements{
	bool if_vbo_init_DrawElements = false;
	GLuint vertId, idxId, colorId;
	// Some method called during initialization
	void initialize_triangle_DrawElemets()
	{
		GLfloat vertices[] = { 0.0, 0.0,
							   0.0, 1.0,
							   1.0, 1.0,
							   1.0, 0.0
		};
		GLfloat colors[] = { 1.0, 0.0, 0.0, 1.0,
							 0.0, 1.0, 0.0, 1.0,
							 0.0, 0.0, 1.0, 1.0,
							 1.0, 1.0, 1.0, 1.0
		};
		GLuint indices[] = { 0, 1, 2, 0, 2, 3};

		glGenBuffers(1, &vertId); //1 means generate 1 buffer                                       // Create buffers
		glBindBuffer(GL_ARRAY_BUFFER, vertId);                                                      // Bind buffers to modify/render them
		glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW); //GL_STREAM_DRAW // Load data into buffers(GPU)
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &colorId);
		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //GL_STREAM_DRAW
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &idxId);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		if_vbo_init_DrawElements = true;
	}


	// 第1种使用vbo的方法：可编程渲染管线
	//note: default setting in OpenGL, vertrex attribute index of pos, normal, color is 0, 2, 3
#define VERTEX_POS_INDX 0
#define VERTEX_NORMAL_INDX 2
#define VERTEX_COLOR_INDX 3

	void DrawVboDisplayCallbackDrawElements(){
		if (!if_vbo_init_DrawElements)
			initialize_triangle_DrawElemets();

		glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableVertexAttribArray(VERTEX_POS_INDX); // 固定管线使用glEnableClientState(GL_VERTEX_ARRAY)
		glVertexAttribPointer(VERTEX_POS_INDX, 2, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glVertexPointer(4, GL_FLOAT, 0, vertexes);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glEnableVertexAttribArray(VERTEX_COLOR_INDX); // 固定管线使用glEnableClientState(GL_COLOR_ARRAY)
		glVertexAttribPointer(VERTEX_COLOR_INDX, 4, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glColorPointer(4, GL_FLOAT, 0, colores);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glDrawElements(GL_TRIANGLES, 2 * 3, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		glDisableVertexAttribArray(VERTEX_POS_INDX);
		glDisableVertexAttribArray(VERTEX_COLOR_INDX);
	}
}
```  
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-2.PNG)

<br />

#### **glDrawArrays和glDrawElements的讨论**

glDrawElements需要有顶点索引数据。这样可以高度共用顶点数据。glDrawArrays对顶点的共用程度取决于mode，GL_TRIANGLES共用程度为0，GL_TRIANGLE_STRIP共用程度高。

<br />

#### **glMultiDrawElements**
glMultiDrawElements是对glDrawElements的进一步优化， 一个glMultiDrawElements命令相当于进行了多个glDrawElements, **减少了CPU对opengl的调用次数**。 

多次调用glDrawArrays

```  c++
//opengl super bible 5th edition 12.4.1 Combining Drawing Functions
for (int i = 0; i < num_objects; i++) 
{
	glDrawArrays(	GL_TRIANGLES,
					object[n]->first_vertex,
					object[n]->vertex_count);
}

```  

优化： glMultiDrawArrays

```  c++
//opengl super bible 5th edition 12.4.1 Combining Drawing Functions
// These arrays are assumed to be sized large enough to hold enough data to
// represent all of the objects in the scene
GLint first[];
GLsizei count[];
// Build our lists of first vertex and vertex count
for (int i = 0; i < num_objects; i++) 
{
	first[i] = object[n]->first_vertex;
	count[i] = object[n]->vertex_count;
}
// Now make a single call to glDrawArrays
glMultiDrawArrays(GL_TRIANGLES, first, count, num_objects);

```  
以上例子中，如果num_objects多达上千次，glMultiDrawArrays将会使CPU对opengl的调用次数将大大减少，那么优化的效果是相当可观的。（待验证）


<br />

#### **glDrawElementsInstanced**
glDrawElementsInstanced是对glDrawElements的进一步优化，**CPU对opengl的调用次数 以及 内存传输 都得到了优化**。 对大量类似对象进行绘制时，glDrawElementsInstanced只需要被调用一次，从CPU内存到GPU内存的传输一个对象数据就可以，其他的变化（旋转，缩放，颜色）可以在shader中进行计算。

以下代码演示了，向GPU传递一个三角形的顶点数据，调用glDrawElementsInstanced得以绘制两个三角形。 首先要绑定一下着色器（shader，program）。 然后在instance.vert中，通过gl_InstanceID来控制这两个三角形的位置。


```  c++
namespace instancerendering{

	GLuint instancingProg = 0;

	char *textFileRead(char *fn)
	{
		FILE *fp;
		char *content = NULL;
		int count = 0;
		if (fn != NULL) {
			fp = fopen(fn, "rt");

			if (fp != NULL) {

				fseek(fp, 0, SEEK_END);
				count = ftell(fp);
				rewind(fp);
				if (count > 0) {
					content = (char *)malloc(sizeof(char)* (count + 1));
					count = fread(content, sizeof(char), count, fp);
					content[count] = '\0';
				}
				fclose(fp);
			}
		}

		if (content == NULL){
			fprintf(stderr, "ERROR: could not load in file %s\n", fn);
			exit(1);
		}
		return content;
	}

	//easy version
	void setShaders() {
		//create --> read --> compile --> attach2program --> linke --> use
		char *vs = NULL, *fs = NULL; //, *fs2 = NULL;
		GLuint v, f;
		v = glCreateShader(GL_VERTEX_SHADER);
		f = glCreateShader(GL_FRAGMENT_SHADER);

		vs = textFileRead("../../shader/instance.vert");
		fs = textFileRead("../../shader/instance.frag");

		glShaderSource(v, 1, &vs, NULL);
		glShaderSource(f, 1, &fs, NULL);

		free(vs); free(fs);

		glCompileShader(v);
		glCompileShader(f);

		instancingProg = glCreateProgram();
		glAttachShader(instancingProg, v);
		glAttachShader(instancingProg, f);

		glLinkProgram(instancingProg);
		glUseProgram(instancingProg);
	}

	//assign shader parameters
	void assignShaderParameters()
	{
		if (instancingProg == 0){
			printf("should use program (glUseProgram) before assign parameters to shader?");
			return;
		}
	}

	bool if_vbo_init = false;
	GLuint vertId, idxId, colorId;
	// Some method called during initialization
	void initialize_triangle()
	{
		GLfloat vertices[] = { 0.0, 0.0,
							   0.0,   1,
							     1,   1
		};
		GLfloat colors[] = { 1.0, 0.0, 0.0, 1.0,
							 0.0, 1.0, 0.0, 1.0,
							 0.0, 0.0, 1.0, 1.0
		};
		GLuint indices[] = { 0, 1, 2 };

		glGenBuffers(1, &vertId); //1 means generate 1 buffer                                       // Create buffers
		glBindBuffer(GL_ARRAY_BUFFER, vertId);                                                      // Bind buffers to modify/render them
		glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW); //GL_STREAM_DRAW // Load data into buffers(GPU)
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &colorId);
		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //GL_STREAM_DRAW
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &idxId);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		if_vbo_init = true;
	}

	bool if_instanced_program_init = false;
	void initialize_instanced_program()
	{
		setShaders();
		assignShaderParameters();
		if_instanced_program_init = true;
	}

#define VERTEX_POS_INDX 0
#define VERTEX_NORMAL_INDX 2
#define VERTEX_COLOR_INDX 3

	void DrawInstancedRendering()
	{
		if (!if_vbo_init)
			initialize_triangle();

		if (!if_instanced_program_init)
			initialize_instanced_program();

		glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableVertexAttribArray(VERTEX_POS_INDX); // 因为使用program， 所以需要使用可编程管线。 固定管线使用glEnableClientState(GL_VERTEX_ARRAY)
		glVertexAttribPointer(VERTEX_POS_INDX, 2, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glVertexPointer(4, GL_FLOAT, 0, vertexes);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glEnableVertexAttribArray(VERTEX_COLOR_INDX); // 因为使用program， 所以需要使用可编程管线。 固定管线使用glEnableClientState(GL_COLOR_ARRAY)
		glVertexAttribPointer(VERTEX_COLOR_INDX, 4, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glColorPointer(4, GL_FLOAT, 0, colores);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		//glDrawElements(GL_TRIANGLES, 1 * 3, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
		glDrawElementsInstanced(GL_TRIANGLES, 1 * 3, GL_UNSIGNED_INT, 0, 2);   //2 means draw 2 primitives
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		glDisableVertexAttribArray(VERTEX_POS_INDX);
		glDisableVertexAttribArray(VERTEX_COLOR_INDX);
	}
}


```  

instance.vert

```  c++
varying vec4 v_color;

void main()
{
	// v_color = vec4(0.0);
	// if(gl_InstanceID==0)
	  // v_color = vec4(1.0, 0.0, 0.0, 1.0);
	// else if(gl_InstanceID==1)
      // v_color = vec4(0.0, 1.0, 0.0, 1.0);
	
	v_color = gl_Color;
	
	//gl_Position = ftransform();
	vec4 p = vec4(0.0);
	if(gl_InstanceID==0)
	  p = vec4(gl_Vertex.x - 0.5, gl_Vertex.y, gl_Vertex.z - 0.5, gl_Vertex.w);
	else if(gl_InstanceID==1)
      p = vec4(gl_Vertex.x + 0.5, gl_Vertex.y, gl_Vertex.z + 0.5, gl_Vertex.w);
	
	gl_Position = gl_ModelViewProjectionMatrix * p;
}

```  


instance.frag

```  c++
varying vec4 v_color;

void main()
{
	gl_FragColor = v_color;
}

``` 
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-0.PNG)


**实例**  
**NDK-samples-teapot-moreteapots**  
todo

**Opengl Programming Guide 8th edition - 3.5 sample**  
todo  


<br />

#### **glDrawElementsBaseVertex**
对glDrawElements的一个改进。增加了GLint basevertex参数，称为**索引基数**，**所有的index数值都要加上这个数值后再进行绘制**。

```c++



```


<br />

#### **glDrawElementsIndrect**
间接绘制函数，绘制参数不是从程序中获得，而是从缓存对象（draw indirect buffer）中获得。 




```c++
typedef  struct {
        uint  vertexCount;
        uint  instanceCount;
        uint  firstIndex;
        uint  baseVertex;
        uint  baseInstance;
    } DrawElementsIndirectCommand

todo

```
http://www.lxway.com/189240081.htm




# 3.图元重启
---
<br />
~~一个模型转换成没有共用顶点的三角形汤(tri soup)时，可以用一个drawElements渲染，但是由于没有重用顶点数据，这样数据量太大了。  一个模型也可以用strip tool转换成一个或多个三角形带(tri strip), 如何才能用一个drawcall渲染多个三角形带呢？ 用图元重启是一种方案。~~

图元重启(Primitive restart) 允许用户用一个drawcall绘制分散的图形。 减少drawcall次数。

mode: GL_TRIANGLE_STRIP, GL_TRIANGLE_FAN, GL_LINE_STRIP, and GL_LINE_LOOP

      不适用与GL_TRIANGLES

使用步骤：  
1.设定PrimitiveRestartIndex来分割原始的indexbuffer。
     
     glPrimitiveRestartIndex(0xFFFFFFFF);


2.draw前，要enable一下：

     glEnable(GL_PRIMITIVE_RESTART);
			glDrawElements(GL_TRIANGLE_STRIP, 9, GL_UNSIGNED_INT, 0);
     glDisable(GL_PRIMITIVE_RESTART);

其中9代表这个indexbuffer有9个index。

代码：

```c++

   3--4
 / | /
1--2
|/ 
0


namespace primitiverestart
{
	bool if_vbo_init = false;
	GLuint vertId, idxId, idxIdLine, colorId;
	// Some method called during initialization
	void initialize_triangle()
	{
		GLfloat vertices[] = { 0.0, 0.0,
							   0.0, 1,
							   1, 1,
							   1, 2,
							   2, 2
		};
		GLfloat colors[] = { 1.0, 0.0, 0.0, 1.0,
							 0.0, 1.0, 0.0, 1.0,
							 0.0, 0.0, 1.0, 1.0,
							 1.0, 1.0, 1.0, 1.0,
							 1.0, 0.0, 1.0, 1.0
		};
		glPrimitiveRestartIndex(0xFFFFFFFF); //during drawing, PrimitiveRestartIndex (0xFFFFFFFF) is not counted into its neighbouring index arays, it breaks the original index array.
		GLuint indices[] = { 0, 1, 2, 3, 0xFFFFFFFF, 1, 2, 3 ,4 };
		GLuint indices_line[] = { 0, 1, 2, 0xFFFFFFFF, 1,3,4 };

		glGenBuffers(1, &vertId); //1 means generate 1 buffer                                       // Create buffers
		glBindBuffer(GL_ARRAY_BUFFER, vertId);                                                      // Bind buffers to modify/render them
		glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW); //GL_STREAM_DRAW // Load data into buffers(GPU)
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &colorId);
		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glBufferData(GL_ARRAY_BUFFER, sizeof(colors), colors, GL_STATIC_DRAW); //GL_STREAM_DRAW
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glGenBuffers(1, &idxId);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		glGenBuffers(1, &idxIdLine);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxIdLine);
		glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices_line), indices_line, GL_STATIC_DRAW);
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		if_vbo_init = true;
	}

	// 第1种使用vbo的方法L：可编程渲染管线
	//note: default setting in OpenGL, vertrex attribute index of pos, normal, color is 0, 2, 3
#define VERTEX_POS_INDX 0
#define VERTEX_NORMAL_INDX 2
#define VERTEX_COLOR_INDX 3

	void DrawVboDisplayCallback1(){
		if (!if_vbo_init)
			initialize_triangle();

		glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableVertexAttribArray(VERTEX_POS_INDX); // 固定管线使用glEnableClientState(GL_VERTEX_ARRAY)
		glVertexAttribPointer(VERTEX_POS_INDX, 2, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glVertexPointer(4, GL_FLOAT, 0, vertexes);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ARRAY_BUFFER, colorId);
		glEnableVertexAttribArray(VERTEX_COLOR_INDX); // 固定管线使用glEnableClientState(GL_COLOR_ARRAY)
		glVertexAttribPointer(VERTEX_COLOR_INDX, 4, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glColorPointer(4, GL_FLOAT, 0, colores);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxId);
		//glDrawElements(GL_TRIANGLES, 1 * 3, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
		glEnable(GL_PRIMITIVE_RESTART);
			glDrawElements(GL_TRIANGLE_STRIP, 9, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		glDisableVertexAttribArray(VERTEX_POS_INDX);
		glDisableVertexAttribArray(VERTEX_COLOR_INDX);
		glDisable(GL_PRIMITIVE_RESTART);
	}

	void DrawVboDisplayCallback2(){
		if (!if_vbo_init)
			initialize_triangle();

		glBindBuffer(GL_ARRAY_BUFFER, vertId);
		glEnableVertexAttribArray(VERTEX_POS_INDX); // 固定管线使用glEnableClientState(GL_VERTEX_ARRAY)
		glVertexAttribPointer(VERTEX_POS_INDX, 2, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glVertexPointer(4, GL_FLOAT, 0, vertexes);
		glBindBuffer(GL_ARRAY_BUFFER, 0);

		//glBindBuffer(GL_ARRAY_BUFFER, colorId);
		//glEnableVertexAttribArray(VERTEX_COLOR_INDX); // 固定管线使用glEnableClientState(GL_COLOR_ARRAY)
		//glVertexAttribPointer(VERTEX_COLOR_INDX, 4, GL_FLOAT, GL_FALSE, 0, 0); // 固定管线使用glColorPointer(4, GL_FLOAT, 0, colores);
		//glBindBuffer(GL_ARRAY_BUFFER, 0);

		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, idxIdLine);
		glEnable(GL_PRIMITIVE_RESTART);
			glDrawElements(GL_LINE_STRIP, 7, GL_UNSIGNED_INT, 0); //1*3 = numb_tri * 3
		glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, 0);

		glDisableVertexAttribArray(VERTEX_POS_INDX);
		glDisableVertexAttribArray(VERTEX_COLOR_INDX);
		glDisable(GL_PRIMITIVE_RESTART);
	}
}
```
![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-5.PNG)

图元重启 和 glMultiDrawElements有很大相似性。只是opengles貌似不支持glMultiDrawElements，但是肯定支持图元重启。


# 4. Batch Rendering
---

<br />

#### **1~3部分所描述的渲染方法有什么潜在的问题？**
关于为何需要Batch Rendering这种技术，可以参考 [^0] 的15.4.2 API Calls 和 [^1]. 首先假定GPU不是瓶颈。 关于Batch Rendering的来由简单来说，cpu执行一次drawcall（glDrawelements）的时间损耗是一定的（无论这个drawcall画多少个三角形），如果CPU有过多的drawcall去执行（drawcall的次数高于CPU的执行效率的极限），便会出现一部分drawcall在CPU执行队列中等待，无法实时将其送到GPU中进行渲染。 那么，要如何减少drawcall的次数呢？ 合并drawcall。 **合并drawcall的过程就是Batch Rendering。**

> The basic idea of batching is to combine a number of objects into a single object, so needing only one API call to render the set.
> ---- Real-Time Rendering, 3rd Edition, p709


什么是一个Batch？ 我的理解 一个Batch是一堆被合并起来的drawcall，以及相关的buffer数据。下文中drawcall==batch。

> Every DrawIndexedPrimitive() is a batch  
> ● Submits n number of triangles to GPU  
> ● Same render state applies to all tris in batch  
> ● SetState calls prior to Draw are part of batch  
> ---- [3] BatchBatchBatch



<br />

#### **CPU的DrawCall的执行效率的极限**
那么我们应该在每一帧调用多少个drawcall（Batch）比较合适呢？

基于[2], p709, Batches Per Frame: 
对于3.7GHz的CPU，40%的使用率，60帧：

<u> <b>25000</b> batches/GHz X <b>3.7</b> GHz X <b>0.4</b> usage</u> 

　　　　　　　　　<b>60</b> fps

其中25000 batches/GHz表示 1GHz的CPU 100%使用时 每秒能执行的drawcall的数量。上述的计算结果是**616**，CPU每一帧执行616个drawcall是极限了。


<br />

#### **GPU的DrawCall的执行效率的极限**
假如现在GPU不再是无限效率，那么CPU每一帧执行的616个drawcall，GPU是否都会实时渲染出来呢？


<br />

#### **每一个Drawcall（Batch）的数据**
How Many Triangles Per Batch?

基于上述对于 CPU和GPU的DrawCall的执行效率的极限 的讨论，一个Batch应该放多少个三角形数据比较合适呢？ 看到有人说不应小于几千个，有的又说少量的只有一两个三角形的batch并不会影响性能。

TODO

<br />

#### **如何合并drawcall？**

合并相同材质的Drawcall

TODO


<br />

#### **实例**
`OpenGL 4.4 Scene Rendering Techniques`  [`LINK`](http://pan.baidu.com/s/1pKHzgWr)

obj : 物体。  
parts：部件。 obj含有多个part， 每个part含有一个材质，两个part可能有相同材质。  
比如，一把菜刀(obj), 含有刀身和手柄两个part，两个part分别是金属和木材两种不同材质。

第:one:种方法，遍历场景中的每个obj。 每个obj中，先设置vb和ib，在设置变换矩阵，然后再遍历这个obj的所有parts。 每个part中，如果材质和前一个part有变化，就变换一下材质，最后drawcall。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-3.PNG)


:arrow_down:

第:two:种方法，对obj中的各个part基于材质进行分组(batch)，把相同材质的part合并为一个材质组，然后各个材质组进行渲染。这样，首先**合并了drawcalls**，减少了drawcall的次数；其次，**减少了由于材质变换引起的context的变换**。

第:two:种方法，遍历场景中的每个obj。 每个obj中，先设置vb和ib，在设置变换矩阵，然后再遍历这个obj的所有 材质组。 每个材质组中，变换一下材质，最后drawcall。

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2017-1-7-opengl-rendering-efficiency-4.PNG)


```  c++

```

# 渲染路径 Rendering Path
---

<br />

#### **ForWard Rendering**




<br />

#### **ForWard+ Rendering**



<br />

#### **Defered Rendering**

overdraw:
屏幕上，新渲染的物体覆盖掉之前渲染的物体。overdraw区域被覆盖的fragment shader的计算浪费了。如果有大量的overdraw或者fragment shader计算量很大，会造成性能消耗。为了解决overdraw，defered shading被提出。

g-buffer：
geometry buffer，stores information about the geometry at that point rather than image properties[super bible]. normal buffer,position buffer,..?



```c++

```

drawback
1.badnwidth. per pixel: 256bit
3x32bit 3x16bit 
pos normal

2.antialiasing 

3.transparency



concept  
Deferred Shading vs Defered Rendering vs Defer Lighting

蓝宝书 Chapter 12: Rendering Techniques 
移动端使用deferred rendering？


<br />

#### **Tiled Based Rendering**


http://www.ceeger.com/Manual/RenderingPaths.html
https://docs.unity3d.com/560/Documentation/Manual/RenderingPaths.html
http://www.tuicool.com/articles/YV7B7j6

http://max.book118.com/html/2014/0426/7926869.shtm
https://www.ishuo.cn/doc/kfplnfqf.html
http://www.klayge.org/2013/04/25/forward%E5%92%8Ctiled-based-deferred%E6%80%A7%E8%83%BD%E6%AF%94%E8%BE%83/
http://twvideo01.ubm-us.net/o1/vault/gdc2012/slides/Programming%20Track/Smedberg_Niklas_Bringing_AAA_Graphics.pdf


# Render Profiling Tools
---

#### **Nsight**



# 附录
---

<br />

#### tips

服务端：渲染流水线  
客户端：CPU内存  
glEnable - glDisable 改变服务端状态  
glEnableClientState - glDisableClientState  改变客户端状态

#### 参考

[^0]: Real-Time Rendering, 3rd Edition [`LINK`](http://www.realtimerendering.com/)  
[^1]: BatchBatchBatch [`LINK`](http://pan.baidu.com/s/1pK9X7xL)



