---
layout: post
title:  "算法导论 视频 笔记"
date:   2017-1-20 00:47:38 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
  - algorithm
  - external
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false


---

http://note.youdao.com/noteshare?id=a40f3a00dc6a33ce15ff83a68fc99295
# Class 1

Two parts:
Part 0.Analysis of algorithm
Part 1.Design of Algorithm

Why performance important?

------------------------------------

Sorting

1. Insertion sort
1.1 what is insertion sort?




1.2 analysis
1.2.1 running time
- depends on input (eg. sorted, )
- depemds on input size (6 elem vs 6*10^9)
 -parameterize in the input size
-want upper bounds
 -guatentee to the user


_Kind of Analysis_

worst case (usually)
T(n) = max time on any input size n

Average case (sometimes)
T(n) = expected time over all inputs of size n
 (need assumption of stastical distribution of input)

Best case (Bogus) (假象)
Cheat



What is the insertion sort time?
 Depends on computer
 - relatibe speed (on same machine)
 - absolute speed (on different machines)
 
BIG IDEA! Asymptonic analysis
1. ignore machine dependent assumption
2. look at growth

Asymptonic notation
theta notation  
 -Drop low order terms
 -Ingore leading constants
Ex. 3n3 + 90n3+5n-100 = theta(n3)
As n->infinite, theta(n2) always beats theta().  Since we look the growth of n.

``` c++
t
|         / n3
|       /
|----------n2
|    /
|  /
|/
|----------------- n
```



_Insertion sort analysis_
Worst case:  input reverse sorted
T(n) = theta(n2) (arithmatic series)

Is insertion sort fast?
 - moderately sp, for small n
 -  not at all for large n
























