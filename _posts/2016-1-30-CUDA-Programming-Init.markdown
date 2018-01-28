---
layout: post
title:  "CUDA编程的一些初级经验"
date:   2016-1-30 20:47:38 +0800
categories: jekyll update
---

詹令   
lealzhan@126.com    
2016.1.30   

# Contents
{:.no_toc}

* Will be replaced with the ToC, excluding the "Contents" header
{:toc}


# **内存结构**
---
<br>

![](https://raw.githubusercontent.com/lealzhan/lealzhan.github.io/master/_pictures/2016-1-30-CUDA-Programming-Init-0.jpg)


# **数据传输**
---
<br>

数据传输是GPU编程非常重要的方面。GPU内存和host内存间的数据传输是比较耗时的（相较于GPU的处理速度），如果GPU内存没有足够的数据量，便会造成GPU闲置。

以下讨论cpu，cuda，Thrust三者间的数据传输。因为cuda，thrust的数据其实都在GPU内存上，所以两者间的数据传输更多是指针的变换。而Thrust，cuda和cpu间的数据传输就要涉及到host内存和GPU内存间的数据转换，会比较耗时。

cpu<——>cuda

```c++
cudaMemcpy(dev_M, M, sizeof(float) * 3 * number, cudaMemcpyHostToDevice);
cudaMemcpy(X, dev_X, sizeof(Scalar) * 3 * number, cudaMemcpyDeviceToHost);
```

cpu<——>Thrust



Thrust <——>cuda ？转换  
Thrust定位于cuda版的stl，包含很多现成的算法，所以适时地使用Thrust是减少自己造轮子的必要手段。曾经看过一本书说正确的GPU程序开发模式应该是，先基于Thrust完成程序的初始版本，然后profile找出瓶颈，然后用cuda 改写Thrust版的瓶颈部分。

```c++
//cuda to thrust
thrust::device_ptr<float> thrust_M((float *)dev_M);

//thrust to cuda
thrust::device_vector<int>a(10000);
thrust::raw_pointer_cast(&a[0]);
```

```c++
//---------------------  summary sample
float* dev_M;
//Allocate data in host memory
M = new float[max_number];
memset(M, 0, sizeof(float)*max_number);

//host to cuda
cudaMemcpy(dev_M, M, sizeof(float) * 3 * number, cudaMemcpyHostToDevice);

//cuda to Thrust: Thrust device pointer
thrust::device_ptr<float> thrust_M((float *)dev_M);
//Thrust reduce
float mass_sum = thrust::reduce(thrust_M + start, thrust_M + start + node_number, (float)0);

//free memory
// no need to free thrust_M?
if (dev_M) cudaFree(dev_M);
if (M) delete[] M;
```

# **thrust 仿函数**
---
<br>

```c++
struct rigid_float3{
	float x[3];
	__host__ __device__
		rigid_float3(){
		for (int i = 0; i < 3; i++)
			this->x[i] = 0;
	};
	__host__ __device__
		rigid_float3(float x, float y, float z){
		this->x[0] = x;
		this->x[1] = y;
		this->x[2] = z;
	};
	__host__ __device__
		rigid_float3 operator+(rigid_float3 p) const {
		return rigid_float3(x[0] + p.x[0], x[1] + p.x[1], x[2] + p.x[2]);
	};
	__host__ __device__
		rigid_float3 operator*(float s) const {
		return rigid_float3(x[0] * s, x[1] * s, x[2] * s);
	};
};


thrust::device_ptr<rigid_float3> thrust_c((rigid_float3 *)dev_c);
rigid_float3 center_3 = thrust::reduce(thrust_c + start, thrust_c + start + node_number, rigid_float3(), thrust::plus<rigid_float3>())*inv_mass_sum;

```


# **cuda kernel 编程**
---
<br>

```c++
__global__ void Kernel_Rigid_Mesh_Center_Calculation(const float* X, const int node_number, const float* M, float *center){
	int i = blockDim.x * blockIdx.x + threadIdx.x;
	if (i >= node_number)	return;

	center[i * 3 + 0] = M[i] * X[i * 3 + 0];
	center[i * 3 + 1] = M[i] * X[i * 3 + 1];
	center[i * 3 + 2] = M[i] * X[i * 3 + 2];
}
```

每个thread会执行一个kernel。让连续的thread（同一个warp）执行kernel时尽量少地读取数据。

流多处理器读取GPU全局内存时，每次读取以1个warp （32个线程）（128byte）( 32个float)为单位。流多处理器向GPU全局内存读取32个float时，如果这32个float是非常分散的，则最多需要读取32次，这将极大降低全局内存数据吞吐量。

对于上述kernel，  连续的thread读取的X，M，center等内存是连续的。
(第0个thread，i = 0, X[0])
(第1个thread，i = 1, X[1])
.
.
.


保持kernel内存连续读取当然是最好，但是GPU缓存其实会在一定程度上弥补非连续内存读取。只有在L1，L2上没有要访问的数据时，才会访问全局内存。  
kernel  
 |  
\/  
L1  
 |  
\/  
L2  
 |  
\/  
全局内存


# Reference
---
<br>

之前写的thrust简单例子  [[LINK](http://com-g.lofter.com/post/1cc9ce73_5585b14)]

