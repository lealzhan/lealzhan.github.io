---
layout: post
title:  "压缩"
date:   2016-8-18 1:11:20 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - 压缩
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
2016.8.18         

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

# Overall
7z 可以压缩整个文件夹，而windows原生压缩工具makecab/expand只能对一个文件压缩解压。   
# 方法

## 7z

### 1. 命令行   
 1. 下载安装包（或者 7z.exe 和 7z.dll）   
 2. 增加7z的安装目录 到 系统环境变量   
     方法一： 手动到控制面板系统属性添加   
     方法二：命令行： path=%path%;C:\Program Files\7-Zip;   
 3. 命令详解   
     7z a folder.zip folder   
     压缩 folder 下的所有文件到 folder.zip   
 4. 如何调用命令行（4.3比较好）
     1. cmd console （黑色对话框）   
     2. bat，人为执行   
     3. c++代码里 调用bat；   
          system("E:\\7z\\my7z.bat");   
          WinExec("E:\\7z\\my7z.bat", SW_HIDE);   
     4. c++代码里 CreateProcess   
          7z.exe 和 7z.dll 放到c++项目中   
          在c++中将 压缩文件参数和7z.exe的路径 传递给 7z的process；    
 
### 2. lib

 
 
## windows原生

压缩一个文件：   
makecab c:/file_name.txt c:/file_name.zip

解压一个文件：   
expand c:/file_name.zip c:/file_name.txt   


## rar



