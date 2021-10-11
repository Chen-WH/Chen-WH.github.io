---
layout:     post
title:      OpenBLAS和LAPACK学习笔记
subtitle:    
date:       2021-10-08
author:     ChenWH
header-img: img/the-first.png
catalog:   true
tags:

    - Note
---

# OpenBLAS+LAPACK

基于OpenBLAS使用LAPACK

## OpenBLAS

### 下载安装

```shell
# 下载源码
git clone git@github.com:xianyi/OpenBLAS.git
# 编译
cd OpenBLAS
make -j12 # 这里j后面的数字是cpu线程数
# 安装
sudo make PREFIX=/opt/OpenBLAS install # 选择安装路径，这里我选择的是/opt/OpenBLAS
```

### 路径配置

```shell
# 找到上一步中设置的安装路径，可以把以下命令加入.profile文件，或者像我一样写一个sh文件每次使用单独source防止污染
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/OpenBLAS/lib
export LIBRARY_PATH=$LIBRARY_PATH:/opt/OpenBLAS/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/opt/OpenBLAS/include
export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/opt/OpenBLAS/include
export OPENBLAS_NUM_THREADS=1 # 指定线程数，建议保持设定为1，最后结论给出原因。
```

### 测试运行

```c
// 调用方式与BLAS一样，可以去Netlib上查找
#include <cblas.h>
#include <stdio.h>

void main() {

    int i = 0;
    double A[6] = {1.0,2.0,1.0,-3.0,4.0,-1.0};         
    double B[6] = {1.0,2.0,1.0,-3.0,4.0,-1.0};  
    double C[9] = {.5,.5,.5,.5,.5,.5,.5,.5,.5}; 

    int M = 3; // row of A and C
    int N = 3; // col of B and C
    int K = 2; // col of A and row of B

    double alpha = 1.0;
    double beta = 0.0;

    cblas_dgemm(CblasRowMajor, CblasNoTrans, CblasNoTrans, M, N, K, alpha, A, K, B, N, beta, C, N);

    for (i = 0; i < 9; i++) {
        printf("%lf ", C[i]);
    }
    printf("\n");
}
```

函数命名为cblas_?????（比如cblas_dgemm）

```shell
# 命令行编译
g++ test.cpp -o test -lopenblas
```

```cmake
# cmake生成
# CMakeLists.txt
cmake_minimum_required(VERSION 3.0.2)
project(eigen_mkl)
# set(CMAKE_BUILD_TYPE "Release" )
# set(CMAKE_BUILD_TYPE "Debug" )
set(CMAKE_CXX_FLAGS "-O3" )

# include 头文件
include_directories
  ${catkin_INCLUDE_DIRS}
)

# 链接库目录
link_directories(
  ${catkin_LIB_DIRS}
 )
 
## 文件名是eigen_mkl.cpp
add_executable(openblas src/openblas.cpp)
# 链接具体的库 libmkl_rt，注意.so是动态库，当然也可以选择静态库，不过文件会大些但更稳定。
target_link_libraries(openblas
-lopenblas
)
```



## LAPACK

### 安装配置

在以下两个网页中选择一个打开下载LAPACK源码并解压

[GitHub链接](https://github.com/Reference-LAPACK/lapack)

[Netlib链接](http://www.netlib.org/lapack/)

```shell
# 在解压后lapack目录下新建build目录并cmake
cd lapack-3.10.0
mkdir build
cd build
cmake ..
# cmake后打开并修改CMakeCache.txt文件
修改　CMAKE_INSTALL_PREFIX:PATH=　等号后面为希望lapack安装的目录，如：CMAKE_INSTALL_PREFIX:PATH=/opt/LAPACK
修改　USE_OPTIMIZED_BLAS:BOOL=　等号后面的OFF修改为　ON，如：USE_OPTIMIZED_BLAS:BOOL=ON
修改　blas_LIB_DEPENDS:STATIC=　等候后面改为OpenBLAS静态库的绝对地址，如：blas_LIB_DEPENDS:STATIC=/opt/OpenBLAS/liboopenblas.a
# 编译安装
保存CMakeCache.txt文件修改
make # 编译
sudo make install # 安装
```

### 路径配置

安装完后安装路径下只有lib文件夹，需要自己添加include并配置路径，include文件夹在原下载路径下

![image-20211011115438531](https://raw.githubusercontent.com/Chen-WH/PicGo/main/Typora/202110111155580.png)

```shell
sudo cp -r ~/Downloads/lapack-3.10.0/LAPACKE/include /opt/LAPACK/

# 添加路径，同上OpenBLAS，这里把两者汇总如下
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/OpenBLAS/lib:/opt/LAPACK/lib
export LIBRARY_PATH=$LIBRARY_PATH:/opt/OpenBLAS/lib:/opt/LAPACK/lib
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/opt/OpenBLAS/include:/opt/LAPACK/include
export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/opt/OpenBLAS/include::/opt/LAPACK/include
export OPENBLAS_NUM_THREADS=1
```

### 测试运行

```c
#include <iostream>
extern "C" {
     void dgesv_(int *n, int *nrhs,  double *a,  int  *lda,  
           int *ipivot, double *b, int *ldb, int *info) ;
}


#define MAX 10

int main(){
   // Values needed for dgesv
   int n;
   int nrhs = 1;
   double a[MAX][MAX];
   double b[1][MAX];
   int lda = MAX;
   int ldb = MAX;
   int ipiv[MAX];
   int info;
   // Other values
   int i,j;

   // Read the values of the matrix
   std::cout << "Enter n \n";
   std::cin >> n;
   std::cout << "On each line type a row of the matrix A followed by one element of b:\n";
   for(i = 0; i < n; i++){
     std::cout << "row " << i << " ";
     for(j = 0; j < n; j++)std::cin >> a[j][i];
     std::cin >> b[0][i];
   }

   // Solve the linear system
   dgesv_(&n, &nrhs, &a[0][0], &lda, ipiv, &b[0][0], &ldb, &info);

   // Check for success
   if(info == 0)
   {
      // Write the answer
      std::cout << "The answer is\n";
      for(i = 0; i < n; i++)
        std::cout << "b[" << i << "]\t" << b[0][i] << "\n";
   }
   else
   {
      // Write an error message
      std::cerr << "dgesv returned error " << info << "\n";
   }
   return info;
}
```

```shell
# 编译选项
g++ test.cpp -o test-llapack -lopenblas -lgfortran -lm
```



实际使用中不需要extern外部引用，加上include后就可以直接调用，函数命名为LAPACK_?????（比如LAPACK_dgesv）

## 性能对比

|  测试项目  | MKL单线程 | MKL多线程 | OpenBLAS+LAPACK单线程 | OpenBLAS+LAPACK多线程 |
| :--------: | :-------: | :-------: | :-------------------: | :-------------------: |
| 84维小矩阵 |  0.385ms  |  0.76ms   |       0.08386ms       |        0.80ms         |

可以看到对于小矩阵运算，OpenBLAS性能明显优于MKL，同时多线程也没有收益，大概是数据同步的损失，基本和洪健的说法以及网上的结论一致。本着严谨治学的态度，先mark以下，后续来补全其他情况的测试。

**结论：小矩阵采用OpenBLAS单线程，大矩阵采用MKL多线程。**
