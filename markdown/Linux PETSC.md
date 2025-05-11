# Cuda和Petsc

## Petsc编译选项
```
#初始选项
./configure --with-cuda=1 --with-debugging=0 --download-f2cblaslapack=1

#调整后选项
./configure --with-cuda=1 --with-debugging=0 --with-zlib=1 --with-blas-lib=/home/cfd/petsc-3.21.4/arch-linux-c-opt/lib/libf2cblas.a  --with-lapack-lib=/home/cfd/petsc-3.21.4/arch-linux-c-opt/lib/libf2clapack.a

```

download-f2cblaslapack库下载地址(F2CBLASLAPACK):
https://web.cels.anl.gov/projects/petsc/download/externalpackages/f2cblaslapack-3.8.0.q2.tar.gz

## WSL2 Cuda介绍

介绍
https://docs.nvidia.com/cuda/wsl-user-guide/index.html

安装教程
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#package-manager-metas

## OpenMPI编译

https://docs.open-mpi.org/en/v5.0.x/tuning-apps/networking/cuda.html

## Petsc示例讲解

https://www.youtube.com/watch?v=VFxhRzmvNgY
```
包含不同模型效率对比(基于snes/tutorials/ex19.c)
CPU and GPU
```

## Petsc报错

https://lists.mcs.anl.gov/pipermail/petsc-users/2015-April/025309.html

https://lists.mcs.anl.gov/pipermail/petsc-users/2011-May/008687.html

https://lists.mcs.anl.gov/pipermail/petsc-users/2011-May/008688.html

https://fenicsproject.org/qa/7877/unable-to-find-requested-pc-type-ml/


