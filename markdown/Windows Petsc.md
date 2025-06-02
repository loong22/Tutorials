Windows平台
---------

petsc官方Windows安装教程地址:

[https://petsc.org/release/install/windows/](https://petsc.org/release/install/windows/)

petsc下载地址:

[https://www.mcs.anl.gov/petsc/mirror/release-snapshots/](https://www.mcs.anl.gov/petsc/mirror/release-snapshots/)

Intel编译套件下载地址:

[https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html](https://www.intel.com/content/www/us/en/developer/tools/oneapi/toolkits.html)

VS2019在线下载地址(在这个页面看到的,下面三个链接直达):

[https://developerinsider.co/download-visual-studio-2019-web-installer-iso-community-professional-enterprise/](https://developerinsider.co/download-visual-studio-2019-web-installer-iso-community-professional-enterprise/)

Visual Studio Community 2019在线安装下载地址:

[https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=16&ref=developerinsider.co](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&rel=16&ref=developerinsider.co)

Visual Studio Professional 2019在线安装下载地址:

[https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Professional&rel=16&ref=developerinsider.co](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Professional&rel=16&ref=developerinsider.co)

Visual Studio Enterprise 2019在线安装下载地址:

[https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Enterprise&rel=16&ref=developerinsider.co](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Enterprise&rel=16&ref=developerinsider.co)


1/6 安装VS2019(包含C++桌面开发)
-----------------------

2/6 安装intel oneAPI 2024基础套件，再安装HPC套件(如果没有检测到VS2019IDE,必须重装VS2019(或者Repair修复安装),检测到了才能安装Intel)
---------------------------------------------------------------------------------------------

```
## 2.1设置Intel的环境变量,必须要,不然后面程序运行会报错或者运行没反应
 
#打开系统属性 -> 环境变量 -> 系统变量 -> PATH
#添加三个路径,置顶
C:\Program Files (x86)\Intel\oneAPI\mpi\2021.12\opt\mpi\libfabric\bin
C:\Program Files (x86)\Intel\oneAPI\mpi\2021.12\bin
C:\Program Files (x86)\Intel\oneAPI\mkl\2024.1\bin
```

3/6 安装cygwin64(要求必须要有python3和make组件)
------------------------------------

```
#请查看官方文档
```

4/6 使用管理员模式打开VS2019 x64 native Tools 命令行, cd打开Cygwin路径下的bin目录，输入mintty.exe打开Cygwin
----------------------------------------------------------------------------------

```
#默认在C盘下
```

5/6 在Cygwin下的编译命令
-----------------

### 5.1先修改Cygwin的环境变量(因为是用VS2019打开的Cygwin)

```
#把/usr/bin/link.exe改名为/usr/bin/ling-cygwin.exe(会和ifort冲突?如果不使用Fortran编译的话无所谓,可以不改)

export PATH=$PATH:/usr/bin/
```

### 5.2把源码包放在/home/[用户名]下,输入下面的命令来解压petsc源码包

```
tar zxvf petsc-3.13.6.tar.gz
```

### 5.3进入解压后的目录,开始configure(configure中配置的路径都是软件安装的默认路径,如果有安装到其他目录要进行修改,--prefix可以自定义安装路径)

```
cd petsc-3.13.6

/usr/bin/python ./configure --with-debugging=0 --with-blaslapack-lib="-L/cygdrive/c/PROGRA~2/Intel/oneAPI/mkl/2024.1/lib mkl_intel_lp64_dll.lib mkl_sequential_dll.lib mkl_core_dll.lib" --with-fc=0 --with-precision="double" --with-mpi-include=/cygdrive/c/PROGRA~2/Intel/oneAPI/mpi/2021.12/include --with-mpi-lib=/cygdrive/c/PROGRA~2/Intel/oneAPI/mpi/2021.12/lib/impi.lib --with-mpiexec="/cygdrive/c/PROGRA~2/Intel/oneAPI/mpi/2021.12/bin/mpiexec.exe -localonly" --prefix="../intel-mpi-petsc-3.13.6-double" --with-shared-libraries=0 

#GPU测试
/usr/bin/python ./configure --with-cuda=1 --with-cudac='win32fe nvcc' --with-cuda-include='[/cygdrive/c/PROGRA~1/NVIDIA~2/CUDA/v12.5/include,/cygdrive/c/PROGRA~1/NVIDIA~2/CUDA/v12.5/include/nvtx3]' --with-cuda-lib='-L/cygdrive/c/PROGRA~1/NVIDIA~2/CUDA/v12.5/lib/x64 cublas.lib cublasLt.lib cuda.lib cudadevrt.lib cudart.lib cudart_static.lib cufft.lib cufftw.lib curand.lib cusolver.lib cusolverMg.lib cusparse.lib'  --with-debugging=0 --with-blaslapack-lib="-L/cygdrive/c/PROGRA~2/Intel/oneAPI/mkl/2024.1/lib mkl_intel_lp64_dll.lib mkl_sequential_dll.lib mkl_core_dll.lib" --with-fc=0 --with-precision="double" --with-mpi-include=/cygdrive/c/PROGRA~2/Intel/oneAPI/mpi/2021.12/include --with-mpi-lib=/cygdrive/c/PROGRA~2/Intel/oneAPI/mpi/2021.12/lib/impi.lib --with-mpiexec="/cygdrive/c/PROGRA~2/Intel/oneAPI/mpi/2021.12/bin/mpiexec.exe -localonly" --prefix="../intel-mpi-petsc-3.13.6-double" --with-shared-libraries=0 


./configure --with-cuda=1 --with-debugging=0 --with-fc=0 --with-precision="double" --with-shared-libraries=0 --prefix="../intel-mpi-petsc-3.13.6-double" --with-mpiexec=mpiexec --with-blaslapack-lib="/home/Admin/mkl/lib mkl_intel_lp64_dll.lib mkl_sequential_dll.lib mkl_core_dll.lib" --with-mpi-include=/home/Admin/mpi/include --with-mpi-lib=/home/Admin/mpi/lib/impi.lib  --with-cudac="win32fe nvcc" --with-cuda-include="[/home/Admin/v12.5/include,/home/Admin/v12.5/include/thrust]" --with-cuda-lib="-L/home/Admin/v12.5/lib/x64 cufft.lib cublas.lib cudart.lib cusparse.lib cusolver.lib cuda.lib"

./configure --with-cuda=1 --with-debugging=0 --with-fc=0 --with-precision="double" --with-shared-libraries=0 --prefix="../intel-mpi-petsc-3.13.6-double" --with-mpiexec=mpiexec --with-mpi-include=/home/Admin/mpi/include --with-mpi-lib=/home/Admin/mpi/lib/impi.lib  --with-cudac="win32fe nvcc" --with-cuda-include=/home/Admin/v12.5/include --with-cuda-lib="-L/home/Admin/v12.5/lib/x64 cudart_static.lib cudadevrt.lib cufft.lib cufftw.lib"


```

### 5.4如果configure成功的话,也会在Cygwin界面上输出下一步make和make install的命令

```
#注意自己所在的真实路径
make PETSC_DIR=/home/[admin]/petsc-3.13.6 PETSC_ARCH=arch-mswin-c-opt all

make PETSC_DIR=/home/[admin]/petsc-3.13.6 PETSC_ARCH=arch-mswin-c-opt install

#下面这两个命令可以不用输入没关系
#/usr/bin/make  --no-print-directory -f makefile PETSC_ARCH=arch-mswin-c-opt PETSC_DIR=/home/admin/petsc-3.13.6 mpi4py-install petsc4py-install libmesh-install mfem-install slepc-install hpddm-install amrex-install
#make PETSC_DIR=/home/admin/3.13.6-intel-mpi PETSC_ARCH="" check
```

6/6 VS2019中项目设置
---------------

### 6.1新建空白C++项目

```

修改C++编译器为Intel C++ 2024(选中项目 -> 鼠标右键 -> Intel Compiler修改, 或者进入项目属性页面进行修改)
#设置Intel Libraries for oneAPI选项(这个选项测试通过,未测试设置为其他选项是否可行)
Use Intel IPP = Static Library
Use oneMKL = Parallel
Use MPI Library = Intel MPI Library
```

### 6.2点击鼠标右键设置VC目录和静态链接库

```
#VC目录设置
C:\Program Files (x86)\Windows Kits\10\Include\10.0.19041.0\ucrt;C:\cygwin64\home\Administrator\intel-mpi-petsc-3.13.6-double\include;C:\Program Files %28x86%29\Intel\oneAPI\mpi\2021.12\include;$(IncludePath)

#静态链接库设置
C:\cygwin64\home\Administrator\intel-mpi-petsc-3.13.6-double\lib\libpetsc.lib;C:\Program Files (x86)\Intel\oneAPI\mpi\2021.12\lib\impi.lib;%(AdditionalDependencies)
```

### 6.3输入下面的示例代码进行编译测试(能输出结果就是可以了)

```cpp
static char help[] = "Solves a linear system in parallel with KSP.\n\n";

/*T
   Concepts: KSP^solving a Helmholtz equation
   Concepts: complex numbers;
   Concepts: Helmholtz equation
   Processors: n
T*/



/*
   Description: Solves a complex linear system in parallel with KSP.

   The model problem:
      Solve Helmholtz equation on the unit square: (0,1) x (0,1)
          -delta u - sigma1*u + i*sigma2*u = f,
           where delta = Laplace operator
      Dirichlet b.c.'s on all sides
      Use the 2-D, five-point finite difference stencil.

   Compiling the code:
      This code uses the complex numbers version of PETSc, so configure
      must be run to enable this
*/

/*
  Include "petscksp.h" so that we can use KSP solvers.  Note that this file
  automatically includes:
     petscsys.h       - base PETSc routines   petscvec.h - vectors
     petscmat.h - matrices
     petscis.h     - index sets            petscksp.h - Krylov subspace methods
     petscviewer.h - viewers               petscpc.h  - preconditioners
*/
#include <petscksp.h>

int main(int argc, char** args)
{
    Vec            x, b, u;      /* approx solution, RHS, exact solution */
    Mat            A;            /* linear system matrix */
    KSP            ksp;         /* linear solver context */
    PetscReal      norm;         /* norm of solution error */
    PetscInt       dim, i, j, Ii, J, Istart, Iend, n = 6, its, use_random;
    PetscErrorCode ierr;
    PetscScalar    v, none = -1.0, sigma2, pfive = 0.5, * xa;
    PetscRandom    rctx;
    PetscReal      h2, sigma1 = 100.0;
    PetscBool      flg = PETSC_FALSE;

    ierr = PetscInitialize(&argc, &args, (char*)0, help); if (ierr) return ierr;
    ierr = PetscOptionsGetReal(NULL, NULL, "-sigma1", &sigma1, NULL); CHKERRQ(ierr);
    ierr = PetscOptionsGetInt(NULL, NULL, "-n", &n, NULL); CHKERRQ(ierr);
    dim = n * n;

    /* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
           Compute the matrix and right-hand-side vector that define
           the linear system, Ax = b.
       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */
       /*
          Create parallel matrix, specifying only its global dimensions.
          When using MatCreate(), the matrix format can be specified at
          runtime. Also, the parallel partitioning of the matrix is
          determined by PETSc at runtime.
       */
    ierr = MatCreate(PETSC_COMM_WORLD, &A); CHKERRQ(ierr);
    ierr = MatSetSizes(A, PETSC_DECIDE, PETSC_DECIDE, dim, dim); CHKERRQ(ierr);
    ierr = MatSetFromOptions(A); CHKERRQ(ierr);
    ierr = MatSetUp(A); CHKERRQ(ierr);

    /*
       Currently, all PETSc parallel matrix formats are partitioned by
       contiguous chunks of rows across the processors.  Determine which
       rows of the matrix are locally owned.
    */
    ierr = MatGetOwnershipRange(A, &Istart, &Iend); CHKERRQ(ierr);

    /*
       Set matrix elements in parallel.
        - Each processor needs to insert only elements that it owns
          locally (but any non-local elements will be sent to the
          appropriate processor during matrix assembly).
        - Always specify global rows and columns of matrix entries.
    */

    ierr = PetscOptionsGetBool(NULL, NULL, "-norandom", &flg, NULL); CHKERRQ(ierr);
    if (flg) use_random = 0;
    else use_random = 1;
    if (use_random) {
        ierr = PetscRandomCreate(PETSC_COMM_WORLD, &rctx); CHKERRQ(ierr);
        ierr = PetscRandomSetFromOptions(rctx); CHKERRQ(ierr);
        ierr = PetscRandomSetInterval(rctx, 0.0, PETSC_INT); CHKERRQ(ierr);
    }
    else {
        sigma2 = 10.0 * PETSC_INT;
    }
    h2 = 1.0 / ((n + 1) * (n + 1));
    for (Ii = Istart; Ii < Iend; Ii++) {
        v = -1.0; i = Ii / n; j = Ii - i * n;
        if (i > 0) {
            J = Ii - n; ierr = MatSetValues(A, 1, &Ii, 1, &J, &v, ADD_VALUES); CHKERRQ(ierr);
        }
        if (i < n - 1) {
            J = Ii + n; ierr = MatSetValues(A, 1, &Ii, 1, &J, &v, ADD_VALUES); CHKERRQ(ierr);
        }
        if (j > 0) {
            J = Ii - 1; ierr = MatSetValues(A, 1, &Ii, 1, &J, &v, ADD_VALUES); CHKERRQ(ierr);
        }
        if (j < n - 1) {
            J = Ii + 1; ierr = MatSetValues(A, 1, &Ii, 1, &J, &v, ADD_VALUES); CHKERRQ(ierr);
        }
        if (use_random) { ierr = PetscRandomGetValue(rctx, &sigma2); CHKERRQ(ierr); }
        v = 4.0 - sigma1 * h2 + sigma2 * h2;
        ierr = MatSetValues(A, 1, &Ii, 1, &Ii, &v, ADD_VALUES); CHKERRQ(ierr);
    }
    if (use_random) { ierr = PetscRandomDestroy(&rctx); CHKERRQ(ierr); }

    /*
       Assemble matrix, using the 2-step process:
         MatAssemblyBegin(), MatAssemblyEnd()
       Computations can be done while messages are in transition
       by placing code between these two statements.
    */
    ierr = MatAssemblyBegin(A, MAT_FINAL_ASSEMBLY); CHKERRQ(ierr);
    ierr = MatAssemblyEnd(A, MAT_FINAL_ASSEMBLY); CHKERRQ(ierr);

    /*
       Create parallel vectors.
        - When using VecCreate(), VecSetSizes() and VecSetFromOptions(),
        we specify only the vector's global
          dimension; the parallel partitioning is determined at runtime.
        - Note: We form 1 vector from scratch and then duplicate as needed.
    */
    ierr = VecCreate(PETSC_COMM_WORLD, &u); CHKERRQ(ierr);
    ierr = VecSetSizes(u, PETSC_DECIDE, dim); CHKERRQ(ierr);
    ierr = VecSetFromOptions(u); CHKERRQ(ierr);
    ierr = VecDuplicate(u, &b); CHKERRQ(ierr);
    ierr = VecDuplicate(b, &x); CHKERRQ(ierr);

    /*
       Set exact solution; then compute right-hand-side vector.
    */

    if (use_random) {
        ierr = PetscRandomCreate(PETSC_COMM_WORLD, &rctx); CHKERRQ(ierr);
        ierr = PetscRandomSetFromOptions(rctx); CHKERRQ(ierr);
        ierr = VecSetRandom(u, rctx); CHKERRQ(ierr);
    }
    else {
        ierr = VecSet(u, pfive); CHKERRQ(ierr);
    }
    ierr = MatMult(A, u, b); CHKERRQ(ierr);

    /* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                  Create the linear solver and set various options
       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */

       /*
          Create linear solver context
       */
    ierr = KSPCreate(PETSC_COMM_WORLD, &ksp); CHKERRQ(ierr);

    /*
       Set operators. Here the matrix that defines the linear system
       also serves as the preconditioning matrix.
    */
    ierr = KSPSetOperators(ksp, A, A); CHKERRQ(ierr);

    /*
      Set runtime options, e.g.,
          -ksp_type <type> -pc_type <type> -ksp_monitor -ksp_rtol <rtol>
    */
    ierr = KSPSetFromOptions(ksp); CHKERRQ(ierr);

    /* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                        Solve the linear system
       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */

    ierr = KSPSolve(ksp, b, x); CHKERRQ(ierr);

    /* - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                        Check solution and clean up
       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - */

       /*
           Print the first 3 entries of x; this demonstrates extraction of the
           real and imaginary components of the complex vector, x.
       */
    flg = PETSC_FALSE;
    ierr = PetscOptionsGetBool(NULL, NULL, "-print_x3", &flg, NULL); CHKERRQ(ierr);
    if (flg) {
        ierr = VecGetArray(x, &xa); CHKERRQ(ierr);
        ierr = PetscPrintf(PETSC_COMM_WORLD, "The first three entries of x are:\n"); CHKERRQ(ierr);
        for (i = 0; i < 3; i++) {
            ierr = PetscPrintf(PETSC_COMM_WORLD, "x[%D] = %g + %g i\n", i, (double)PetscRealPart(xa[i]), (double)PetscImaginaryPart(xa[i])); CHKERRQ(ierr);
        }
        ierr = VecRestoreArray(x, &xa); CHKERRQ(ierr);
    }

    /*
       Check the error
    */
    ierr = VecAXPY(x, none, u); CHKERRQ(ierr);
    ierr = VecNorm(x, NORM_2, &norm); CHKERRQ(ierr);
    ierr = KSPGetIterationNumber(ksp, &its); CHKERRQ(ierr);
    if (norm < 1.e-12) {
        ierr = PetscPrintf(PETSC_COMM_WORLD, "Norm of error < 1.e-12 iterations %D\n", its); CHKERRQ(ierr);
    }
    else {
        ierr = PetscPrintf(PETSC_COMM_WORLD, "Norm of error %g iterations %D\n", (double)norm, its); CHKERRQ(ierr);
    }

    /*
       Free work space.  All PETSc objects should be destroyed when they
       are no longer needed.
    */
    ierr = KSPDestroy(&ksp); CHKERRQ(ierr);
    if (use_random) { ierr = PetscRandomDestroy(&rctx); CHKERRQ(ierr); }
    ierr = VecDestroy(&u); CHKERRQ(ierr); ierr = VecDestroy(&x); CHKERRQ(ierr);
    ierr = VecDestroy(&b); CHKERRQ(ierr); ierr = MatDestroy(&A); CHKERRQ(ierr);
    ierr = PetscFinalize();
    return ierr;
}


/*TEST

   build:
      requires: complex

   test:
      args: -n 6 -norandom -pc_type none -ksp_monitor_short -ksp_gmres_cgs_refinement_type refine_always

   testset:
      suffix: deflation
      args: -norandom -pc_type deflation -ksp_monitor_short
      requires: superlu_dist

      test:
        nsize: 6

      test:
        nsize: 3
        args: -pc_deflation_compute_space {{db2 aggregation}}

      test:
        suffix: pc_deflation_init_only-0
        nsize: 4
        args: -ksp_type fgmres -pc_deflation_compute_space db4 -pc_deflation_compute_space_size 2 -pc_deflation_levels 2 -deflation_ksp_max_it 10
        #TODO remove suffix and next test when this works
        #args: -pc_deflation_init_only {{0 1}separate output}
        args: -pc_deflation_init_only 0

      test:
        suffix: pc_deflation_init_only-1
        nsize: 4
        args: -ksp_type fgmres -pc_deflation_compute_space db4 -pc_deflation_compute_space_size 2 -pc_deflation_levels 2 -deflation_ksp_max_it 10
        args: -pc_deflation_init_only 1

TEST*/


```

### 6.4Done

Ubuntu安装
--------

Cuda With Petsc

1\. 安装mvapich-2.3.7
-------------------

官方下载地址 [https://mvapich.cse.ohio-state.edu/downloads/](https://mvapich.cse.ohio-state.edu/downloads/)

安装教程 [https://mvapich.cse.ohio-state.edu/static/media/mvapich/mvapich2-quickstart.html](https://mvapich.cse.ohio-state.edu/static/media/mvapich/mvapich2-quickstart.html)

psm源码下载地址 [https://www.openfabrics.org/downloads/infinipath-psm/](https://www.openfabrics.org/downloads/infinipath-psm/)

2\. 安装cuda-tool kit
-------------------

[https://developer.nvidia.com/cuda-downloads?target\_os=Linux&target\_arch=x86\_64&Distribution=WSL-Ubuntu&target\_version=2.0&target\_type=deb\_local](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_local)

```sh
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pinsudo

mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600

wget https://developer.download.nvidia.com/compute/cuda/12.5.1/local_installers/cuda-repo-wsl-ubuntu-12-5-local_12.5.1-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-5-local_12.5.1-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-5-local/cuda-*-keyring.gpg /usr/share/keyrings/

sudo apt-get updatesudo

apt-get -y install cuda-toolkit-12-5
```

或者

```sh

wget https://developer.download.nvidia.com/compute/cuda/12.5.1/local_installers/cuda_12.5.1_555.42.06_linux.runsudo sh 

cuda_12.5.1_555.42.06_linux.run
```

3\. 安装petsc
-----------

Configure

```sh
tar zxvf petsc-3.21.3.tar.gz
cd petsc-3.21.3
tar zxvf pkg-fblaslapack.tar.gz

#configure
./configure --prefix=/home/cfd/bin/petsc-3.21.3 --with-cuda=1 --with-blas-lib=<blas_lib> --with-debugging=0

#online
./configure --prefix=/home/cfd/bin/petsc-3.21.3 --with-cuda=1 --download-fblaslapack=1 --with-debugging=0
```

make

```sh
make PETSC_DIR=/home/cfd/petsc-3.21.3 PETSC_ARCH=arch-linux-c-debug all

make PETSC_DIR=/home/cfd/petsc-3.21.3 PETSC_ARCH=arch-linux-c-opt all
```

make install

```sh
make PETSC_DIR=/home/cfd/petsc-3.21.3 PETSC_ARCH=arch-linux-c-debug install

make PETSC_DIR=/home/cfd/petsc-3.21.3 PETSC_ARCH=arch-linux-c-opt all
```