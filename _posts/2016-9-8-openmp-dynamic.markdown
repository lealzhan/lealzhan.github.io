---
layout: post
title:  "openmp parallel for schedule(dynamic)"
date:   2016-9-8 22:28:18 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - numerical method
  - parallel computing
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
2016.9.8       

```c++
#pragma omp parallel for
for()
{

}
```

==>

```c++
#pragma omp parallel for schedule(dynamic)
for()
{

}
```

简单说，原先的方法只会在for开始时，将for循环分配给各个线程（默认是schedule(static)）。如果某个线程运行较其他线程早结束，系统也不会把其他线程的任务分配给它，这就造成了线程的闲置。

加了schedule(dynamic)后，就会动态分配任务给各个线程了。如果有个线程先完成任务，那么系统就会分配更多的任务给他，这样就不会造成线程闲置。

## 参考   

http://www.cnblogs.com/xudong-bupt/p/3622101.html





