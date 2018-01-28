---

layout: post
title:  "多元函数的极值"
date:   2017-8-14  23:1:38 +0800
categories: jekyll update


# tags will also be used as html meta keywords.
tags:
  - numerical method
  - optimization
show_meta: true
comments: true
mathjax: true
gistembed: true
published: true
noindex: false
nofollow: false
use_math: true

---

詹令   
lzhan@alumni.cmu.edu    
2017.8.14   
last modified 2017.10.30

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}




# **Unconstrained**

多维无约束优化问题:   

$$ \min_{x \in R^n} f(x) $$

最优化方法主要是基于迭代方法。 迭代方法要求迭代过程中目标函数是下降的:

$$ f(x_k+1)<f(x_k) $$

**迭代方法的基本要素**

- 搜索方向
- 步长
- 迭代格式
- 终止条件

**迭代方法的基本流程**



**两大类迭代方法**

- 线搜索    
  先计算搜索方向，再计算步长   
  最速下降，共轭梯度，牛顿法，伪牛顿法，
  又分为精确线搜索和非精确线搜索。   
  	 - 精确线搜索一般不用
  	 - 非精确线搜索又分为好多种。
      - Arjimo
      - Goldstein 
 
- 信赖域   
  先计算步长，再计算搜索方向

## **1. Line Search 线搜索**

### **1.1 基于解析梯度**

#### **1.1.1 Gradient Descent/Stepest Descent**:       
##### **1.1.1.1 特性**
- 多维无约束基于梯度的优化算法中**最简单，可靠**的方法。   
- 收敛速度较慢，**一次收敛**。
- 越接近越接近目标,步长越小。步长选择对结果影响很大。BB步长？
- 可用于求解非线性方程组?   

##### **1.1.1.2 算法**
**第k步迭代时的搜索方向**

$$ \min_{d_k} f(x_k + d_k)$$


对$$ f(x_k + d_k)$$ 一阶泰勒展开:

$$ f(x_k + d_k) = f(x_k) + \nabla f(x_k)^T d_k $$

等价于求

$$ \min_{d_k} \left(f(x_k) + \nabla f(x_k)^T d_k \right)$$

== 

求$$d_k$$使其在$$\nabla f(x_k))$$上的投影最小. 显然，当$$\color{red}{d_k = -\nabla f(x_k)}$$ 时, 投影最小(-1)。


**x的搜索方向**   

$$ \color{red}{d_k= -\nabla f(x_k) } $$

**步长** $$ \alpha_k $$  由一维搜索来确定:   

$$ f(x_k + \alpha_k d_k) = \min_{\alpha > 0} f(x_k + \alpha d_k) $$

**迭代格式**   

$$ x_{k+1} = x_k + \alpha_k d_k  $$


**终止条件**   

$$ \Delta f(x)<\varepsilon $$   

或者

$$ ||d_k||_2 < \varepsilon $$


？以上的梯度如果是基于f的解析梯度求得，则是最原始的梯度下降。如果是基于f(x)的样本点求得，则分为以下三种情况:批量梯度下降法, 随机梯度下降,小批量梯度下降法。？   
##### **1.1.1.3 困难点及解决方案** 

##### **1.1.1.4 变种** 

###### **1.1.1.4.1 批量梯度下降法** 

(Batch Gradient Descent, BGD)

###### **1.1.1.4.2 随机梯度下降**

(Stochastic gradient descent, SGD)
http://www.cnblogs.com/maybe2030/p/5089753.html
相对于MGD更常用些。
最早是在Tom Mitchel的机器学习[]的讲人工神经网络时看到的.

###### **1.1.1.4.3 小批量梯度下降法**

（Mini-batch Gradient Descent，MBGD）

##### **1.1.1.5 并行的问题**



#### **1.1.2 Linear Conjugate Gradient**
##### **1.1.2.1 特性**

##### **1.1.2.2 算法**
##### **1.1.2.3 困难点**
##### **1.1.2.4 解决方案**


目标函数是**凸/正定**的**二次**函数的情形,求以下函数$$ f(x) $$的最小值：

$$ f(x) = \ g^Tx  + \frac{1}{2}x^THx $$

其中$$ g \in R^n, H \in R^{n \times n}对称正定 $$。
等价于求$$Hx+g=0$$.

let $$g_k = \nabla f(x_k) = Hx_k+g$$


**步长**

$$ \alpha_k = -\frac{g_k^Td_k}{d_k^THd_k}$$ 

**迭代格式**   

$$ x_{k+1} = x_k + \alpha_k d_k  $$

**搜索方向**

$$ d_{k+1} = - g_{k+1} + \beta_k d_{k} $$

其中$$ \beta_k = \frac{g_{k+1}^T Hd_k}{d_k^THd_k} $$
或者 $$ \beta_k = \frac{g_{k+1}^T Hd_k}{d_k^THd_k} $$


preconditioned conjugate gradient?


#### **1.1.3 None-linear Conjugate Gradient methods**    
目标函数是**凸**的一般函数的情形, 求以下函数$$ f(x) $$的最小值：

$$ f(x)$$

$$ \beta_k $$的选取和Linear CG不同

storage of order N
##### **1.1.3.1 Fletcher-Reeves algorithm**    
   the original none-linear cg     
##### **1.1.3.2 Polak-Ribiere algorithm**    
   need 1st derivative    
   new gamma_i    
   (scipy.optimize.minimize "CG")

**搜索方向**   

#### **1.1.4 Newton Raphson Method** 
##### 特性

- 要求目标函数二次可微
- 要求Hessian矩阵正定
- 迭代求求解时 二次收敛

##### 算法
**第k步迭代时的搜索方向**

$$ \min_{d_k} f(x_k + d_k)$$


对$$ f(x_k + d_k)$$ 一阶泰勒展开:

$$ f(x_k + d_k) = f(x_k) + \nabla f(x_k)^T d_k $$

等价于求

$$ \min_{d_k} \left(f(x_k) + \nabla f(x_k)^T d_k \right)$$

== 

求$$d_k$$使其在$$\nabla f(x_k))$$上的投影最小. 显然，当$$\color{red}{d_k = -\nabla f(x_k)}$$ 时, 投影最小(-1)。


对$$ f(x_k + d_k)$$ 二阶泰勒展开:

$$ f(x_k + d_k) = f(x_k) + \nabla f(x_k)^T d_k + \frac{1}{2}d_k^T\nabla^2f(x_k)d_k $$

等价于求

$$ \min_{d_k} \left( f(x_k) + \nabla f(x_k)^T d_k + \frac{1}{2}d_k^T\nabla^2f(x_k)d_k \right)$$

上式中的目标函数对$$d_k$$的梯度是零：    

$$\nabla f(x_k)^T  + \nabla^2f(x_k)d_k = 0$$

$$ \color{red}{d_k = -[\nabla^2f(x_k)]^{-1}\nabla f(x_k)^T  }$$


---------

求目标数$$f(x)$$的最小值。基于迭代法，等价于求第k步迭代时目标函数在哪个方向上

$$ \min_{d \in R^n} {f(x_k+d)} $$

假定 $$f(x)$$ 二次可微， 且 Hessian矩阵 $$ \nabla^2 f(x)$$ 正定。   
将$$f(x)$$在当前迭代点$$x_k$$上泰勒展开,

$$ f(x_k+d) \approx \ f(x_k) + d^T \nabla f(x_k) + \frac{1}{2}d^T \nabla^2 f(x_k)d $$

$$ \Longrightarrow  $$

$$ \min_{d \in R^n} {(f(x_k+d))} $$ 等价于泰勒展开的右边项$$f(x_k) + d^T \nabla f(x_k) + \frac{1}{2}d^T \nabla^2 f(x_k)d$$对$$d$$的梯度为零

$$ \nabla f(x_k) + \nabla^2 f(x_k)d = 0 $$

因为 $$ \nabla^2 f(x)$$ 正定[]， 可得**搜索方向**

$$ \color{red}{d_k=-[\nabla^2 f(x_k)]^{-1}\nabla f(x_k) } $$

**步长** $$ \alpha_k $$  由一维/线性搜索来确定:   
待定
$$ f(x_k + \alpha_k d_k) = \min_{\alpha > 0} f(x_k + \alpha d_k) $$

**迭代格式**   

$$ x_{k+1} = x_k + \alpha_k d_k  $$

**终止条件**   
待定

$$ \Delta f(x)<\varepsilon $$   

或者

$$ ||d_k||_2 < \varepsilon $$

##### 困难点

- Hessian矩阵难以求出。什么情况下难以求出？鞍点？
- Hessian矩阵可以求出，但是Hessian矩阵的逆可能数值不稳定.

##### 解决方案

- 截尾牛顿法
- 修正牛顿法
- 拟牛顿法


##### 并行的问题



#### **1.1.5 Quasi-Newton**/Pseudo-Newton/拟牛顿法    

- 超线性收敛 superlinear convergence

$$ \nabla^2 f(x)$$ 其实不一定正定（马鞍面？），所以Newton Raphson Method的$$d_k$$不一定是最优方向。但是可以构造出一个正定的$$ \nabla^2 f(x)$$, 便有了Quasi-Newton？

$$ f(x_k+d) \approx \ f(x_k)+d^T g_k + \frac{1}{2}d^T B_k d $$    

其中$$ g_k= \nabla f(x_k), B_k R^{n \times n} $$是对称矩阵。
可以发现 $$B_k$$被用于近似Newton Raphson Method中的目标函数的海森矩阵$$\nabla^2 f(x_k)$$.

mostly better than Conjugate gradient methods， storage of order N2

##### **1.1.5.1 Davidon-Fletcher-Powell (DFP) algorithm**


##### **1.1.5.2 Broyden-Fletcher-Goldfarb-Shanno (BFGS) algorithm**    

- even good for non-smooth optimization   
   - need 1st derivative(gradient) (analytically or numerically)
   - 2nd derivative(hessian matrix) is approximated and stored;  =>?  storage of order N2
   - better than DFP; 
   - superlinear convergence(< quadritic)    
   - (scipy.optimize.minimize "BFGS")    
   - 变种： **L-BFGS** (Low-Storage BFGS) (Limited-Memory BFGS)    
     - [[LINK](https://nlopt.readthedocs.io/en/latest/NLopt_Algorithms/#Low-storage_BFGS)] [Numerical Optimization 2nd 9.1]    
     - 用于大规模问题     
     - 只存储 少量长度为n的向量 来近似 n*n的Hessian矩阵 =>?  storage of order N    
     - linear convergency    
     - (scipy.optimize.minimize "L-BFGS-B")

$$ H = \nabla^2 f(x_k) \leftarrow B $$    

其中 

$$ B_0 = I $$

$$ B_{k+1} = B_k + \frac{\Delta_kx \Delta_k^Tx}{\Delta_k^Tx \Delta_k\theta} + \frac{B_k \Delta_kx(B_k\Delta_kx)^T}{\Delta_k^Tx B_k \Delta_kx} $$

**搜索方向**   

$$ \color{red}{d_k= - B_k^{-1}\nabla f(x_k)}  $$

> 拟牛顿法和牛顿法一样，都会碰到一个工程上的挑战：当x的维度很高时，H就会变得非常大。比方说x是一百万维，H就有一万亿个元素，一般计算机的内存可对付不了这么大的矩阵。于是，还要把H做个近似，将其分解为两个条状矩阵和一个对角阵，这样规模就大大降低了。如果在BFGS方法上应用这一技巧，就是著名的L-BFGS方法，这里的“L”，指的是“Limited-Memory”。
> http://www.sohu.com/a/154817861_647918

> 梯度下降法和牛顿法/拟牛顿法相比，两者都是迭代求解，不过梯度下降法是梯度求解，而牛顿法/拟牛顿法是用二阶的海森矩阵的逆矩阵或伪逆矩阵求解。相对而言，使用牛顿法/拟牛顿法收敛更快。但是每次迭代的时间比梯度下降法长。 http://www.cnblogs.com/pinard/p/5970503.html

### **1.2 基于目标函数的有限差分式计算数值梯度**
- **Quasi-Newton** or variable metric
- **Broyden-Fletcher-Goldfarb-Shanno (BFGS) algorithm**     
   (scipy.optimize.minimize "BFGS", if deritive func is not given, use numerical deritive)
                                                                                                                                                                                                                                                                             
### **1.3 无梯度/直接法**
无约束优化问题   

$$ \min_{x \in R^n} f(x) $$

等价于求$$\nabla f(x)=0 ?$$.

#### **1.3.1 Nelder-Mead**/**Downhill Simplex Method**
##### **1.3.1.1 特性**
   - **slow**, **robust**, storage of order N2    
   - (scipy.optimize.minimize "Nelder-Mead")
   - 二维空间 三个点， 而爬N维空间里的山峰，就得N+1个人配合了
   
#### **1.3.2 Powell's Method**/**Direction Set Method**    
##### **1.3.2.1 特性**
   - quadratic convergence(?), robust, storage of order N2   
   - (scipy.optimize.minimize "Powell")

储存复杂度  N2和N，N为空间维度。（不是很理解） N代表优化变量的数目？

## **2. Trust Region 置信域法**

> 说到这儿，细心的读者可能会发现一个问题，不论是梯度法、牛顿法、BFGS还是L-BFGS，都可以总结为“先方向，后步长”的方法。但是在找方向的过程中，又是编二阶导，又是近似的，这个方向找的还靠谱么？我只能说，大致上靠谱，不过确实也打了不小的折扣。那么如果不做这些妥协和近似行不行呢，办法也是存在的，不过这就要要变成“先步长，后方向”的思路。
> 先方向后步长方法的典型代表，是置信域法(Trust-region method)，与前面的拟牛顿法相比，它不用近似一个正定的H——这里的妙处在于，只要用金箍棒划了一个圈儿，不管圈儿里是抛物面还是马鞍面，最高点是可以求出来的，而且有个接近闭式的解！
> 不管是先步长还是先方向，步长到底选多大合适呢？说实话，这基本上是个玄学问题（如果不是神学问题的话）：步子迈小了，得多怎才能爬上去；步子迈大了，象毗湿奴那样一下子迈过三界的话，什么梯度、Hession矩阵就全乱了，优化过程也变得不可控。但是，规律还是有的，那就是步长总是要按一定的规律逐渐变小的，好比你罚点球助跑的时候，前面大步流星，后面碎步紧倒，才能准确找到球。 http://www.sohu.com/a/154817861_647918


# **Constrained：**

有约束的优化问题，可以通过**拉格朗日乘数法**转化成无约束的方程组优化问题



## Test Code
### Test Code 0
```python
# python 3.5
# https://docs.scipy.org/doc/scipy/reference/generated/scipy.optimize.minimize.html#scipy.optimize.minimize
from scipy.optimize import minimize, rosen, rosen_der
import numpy as np
#x0 = [1.2, 1.5, 0.39, 1.33]
#res = minimize(rosen, x0, method='Nelder-Mead', tol=1e-4)
#print res.x


def func(x0):
	return (x0[0]-1)*(x0[0]-1) + x0[1]*x0[1] + x0[2]*x0[2] + np.abs(x0[3]*x0[0]) + np.abs(x0[2]*x0[0])

def test():
	x0 = [6.9, 1.1, 1.1, 0.1]

	res = minimize(rosen, x0, method='Nelder-Mead', tol=1e-8)
	print ("Nelder-Mead")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	res = minimize(rosen, x0, method='Powell', tol=1e-8)
	print ("Powell")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	res = minimize(rosen, x0, method='CG', tol=1e-8)
	print ("CG")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	res = minimize(rosen, x0, method='BFGS', tol=1e-8)
	print ("BFGS")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	#need jacobian
	#res = minimize(rosen, x0, method='Newton-CG', tol=1e-8)
	#print ("Newton-CG", "result", res.x, "iteration numb", res.nit

	res = minimize(rosen, x0, method='L-BFGS-B', tol=1e-8)
	print ("L-BFGS-B")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	res = minimize(rosen, x0, method='TNC', tol=1e-8)
	print ("TNC")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	res = minimize(rosen, x0, method='COBYLA', tol=1e-8)
	print ("COBYLA")
	print ("result", res.x)
	print ("iteration numb")#, res.nit\
	print ("----------")

	res = minimize(rosen, x0, method='SLSQP', tol=1e-8)
	print ("SLSQP")
	print ("result", res.x)
	print ("iteration numb", res.nit)
	print ("----------")

	#need jacobian
	#res = minimize(rosen, x0, method='dogleg', tol=1e-8)
	#print ("dogleg", "result", res.x, "iteration numb"

	#need jacobian
	#res = minimize(rosen, x0, method='trust-ncg', tol=1e-8)
	#print ("trust-ncg", "result", res.x, "iteration numb"

if __name__=='__main__':
	test()

```




# Note
Gradient, Jacobian, Hessian

正定矩阵 可逆？

多元函数极值和最小二乘问题的关系



-----
# Reference    
[] Nocedal, Jorge, and S. J. Wright. "Numerical Optimization." Springer 9.4(1999):1556-1556.   
[] python调用 [[LINK](https://docs.scipy.org/doc/scipy/reference/generated/   scipy.optimize.minimize.html#scipy.optimize.minimize)]   
[] Powell's Method[[LINK](http://blog.sina.com.cn/s/blog_3ecbcc070102wvu7.html)]    
[] NLopt [[LINK](http://ab-initio.mit.edu/wiki/index.php/NLopt_Algorithms)]   
[] nonelinear conjugate gradient methods [[wiki](https://en.wikipedia.org/wiki/Nonlinear_conjugate_gradient_method)]   
[] ZOOpt 无梯度优化工具包  [[github](https://github.com/eyounx/ZOOpt)]   
[] stockoverflow : CG vs BFGS [[LINK](https://scicomp.stackexchange.com/questions/507/bfgs-vs-conjugate-gradient-method)]   
[] Comparison of derivative-free optimization algorithms [[LINK](http://archimedes.cheme.cmu.edu/?q=dfocomp)]   

[] 梯度下降法和共轭梯度法有何异同？[[LINK](https://www.zhihu.com/question/27157047/answer/43085778)]
[] http://www.sohu.com/a/154817861_647918

