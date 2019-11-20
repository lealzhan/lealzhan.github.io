---
layout: post
title:  "编程：简单的BLAS调用"
date:   2016-6-30 12:04:00 +0800
categories: jekyll update

# tags will also be used as html meta keywords.
tags:
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
2016.6.30       

## Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}

## 介绍

todo
   
## Code

``` c++

//1. 创建N*N的矩阵
xuscr_begin() point (scalar) construction
// create an N by N sparse matrix
// this will give Q a nonnegative number (the matrix ID)
Q = BLAS_duscr_begin(N,N);

//2.  
xuscr_insert_entry() insert single (i,j) value
// Stick local matrix into global matrix
// this is not implemented in my BLAS:
//   BLAS_duscr_insert_clique(Q, 4, 4, &localQ[0][0], 1, 1, idx, idx);
// so I wrote this code instead:
for (int i=0; i<4; ++i) {
for (int j=0; j<4; ++j) {
BLAS_duscr_insert_entry(Q, localQ[i][j], idx[i], idx[j]);

//3.
xuscr_end() terminate matrix construction
BLAS_duscr_end(Q);

//4. Scale Matrix Vector
// fex += ke*Q*x, fey += ke*Q*y, fez += ke*Q*z
// where ke = elastic stiffness, 3 is offset in vector/matrix
BLAS_dusmv(blas_no_trans, ke, Q, x, 3, fe, 3);

//5.  
usds() destroy matrix
BLAS_usds(Q);

//---------------------------------------------------------
//示例

#include "blas_sparse.h"
#include "stdio.h"
int main(){
const int N = 4;
const int nx = 6;
double val[] = {1.1,2.2,2.4,3.3,4.1,4.4};
int indx[] = {0,1,1,2,3,3};
int jndx[] = {0,1,3,2,0,3};
double x[] = {1.0,1.0,1.0,1.0};
double y[] = {0.0,0.0,0.0,0.0};
// this is the solution
blas_sparse_matrix A;
int i;
double alpha = 1.0;
// Step 1: Create Sparse BLAS Handle
A = BLAS_duscr_begin(N,N);   //double precision, unstructured, sparse, creation, routine
//Step 2: Insert Entries
for (i=0;i<nx;i++)
  BLAS_duscr_insert_entry(A,val[i],indx[i],jndx[i]);
//Step 3: Complete construction of sparse matrix
BLAS_duscr_end(A);
//Step 4: Compute matrix vector product y=A*x
BLAS_dusmv(blas_no_trans,alpha,A,x,1,y,1);  //double precision, unstructured, sparse, matrix, vector
//Step 5: release matrix handle
BLAS_usds(A);                    //unstructured, sparse, destruction, s
printf("The solution is:\n");
for (i=0;i<N;i++)
  printf("%f, ",y[i]);        
return 0;
}

```


