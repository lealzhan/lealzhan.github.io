---
layout: post
title:  "实时渲染：Urho3D 资料"
date:   2016-3-23 18:37:12 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - game engine
  - computer graphics
  - animation
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
2016.3.23       

## Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


## 教程
[`habrahabr.ru`](https://habrahabr.ru/) 上的俄文资料 [`0`](https://habrahabr.ru/post/265611/) [`1`](https://habrahabr.ru/post/265749/) [`2`](https://habrahabr.ru/post/265837/) 英文 [`0`](http://developers-club.com/posts/265749/) [`1`](http://developers-club.com/posts/265837/)

## 代码
下载[`GIT`](https://github.com/1vanK/Urho3DHabrahabr02)的实例代码 在Urho3D Player上打开 Level01.xml   

## 学到的点
#### 1. 如何开启阴影
Lighting 要设置 Cast Shadow 为 true。   
物体也要设置 Cast Shadow 为 true。   

#### 2. 如何开启物体的碰撞
在需要被碰撞检测的物体的同一个node下，添加 RigidBody 和 CollisionShape 组件。
当物体速度很快时，设置RigidBody 组件下的CCD参数。（默认是不开启CCD？设置一下参数，就开启了？）

#### 3. 如何在Urho3D Editor中使用ScriptInstance？

（1）在节点下创建一个ScriptInstance组件，导入AS脚本。输入脚本里的class的名称 Cannon。   

（2）AS 脚本   

``` c++

class Cannon : ScriptObject
{
    // Some Variables
    int direction = 1;

    // This function is called every frame.
    void Update(float timeStep)
    {
            //更新 脚本从属的节点的信息
            float pitch = node.rotation.pitch;
             ...
             ...
            node.rotation = Quaternion(pitch, 0.0f, 0.0f);
    }
}

```




