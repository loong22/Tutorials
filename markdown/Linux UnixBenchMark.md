## Shell命令
```bash
wget https://github.com/kdlucas/byte-unixbench/archive/v5.1.3.tar.gz

tar -zxvf v5.1.3.tar.gz

cd byte-unixbench-5.1.3/UnixBench

make./Run
```
## 测试项简单说明

**Dhrystone 2 using register variables**

此项用于测试 string handling，因为没有浮点操作，所以受软件和硬件设计、编译和链接、代码优化、对内存的缓存、等待状态、整数数据类型的影响。

**Double-Precision Whetstone**

这一项测试浮点数操作的速度和效率。这一测试包括几个模块，每个模块都包括一组用于科学计算的操作。覆盖面很广的一系列 c 函数：sin，cos，sqrt，exp，log 被用于整数和浮点数的数学运算、数组访问、条件分支和程序调用。此测试同时测试了整数和浮点数算术运算。

**Execl Throughput**

测试每秒钟可以执行的execl系统调用的次数。 execl系统调用是exec函数族的一员。它和其他一些与之相似的命令一样是execve()函数的前端。

**File copy**

测试从一个文件向另外一个文件传输数据的速率。每次测试使用不同大小的缓冲区。这一针对文件 read、write、copy 操作的测试统计规定时间（默认是 10s）内的文件 read、write、copy 操作次数。

**Pipe Throughput**

管道是进程间交流的最简单方式，这里的 Pipe throughtput 指的是一秒钟内一个进程可以向一个管道写 512 字节数据然后再读回的次数。需要注意的是，pipe throughtput 在实际编程中没有对应的真实存在。

**Pipe-based Context Switching**

这个测试两个进程（每秒钟）通过一个管道交换一个不断增长的整数的次数。这一点很向现实编程中的一些应用，这个测试程序首先创建一个子进程，再和这个子进程进行双向的管道传输。

**Process Creation**

测试每秒钟一个进程可以创建子进程然后收回子进程的次数（子进程一定立即退出）。一般说来，这个测试被用于对操作系统进程创建这一系统调用的不同实现的比较。

**Shell Scripts**

测试一秒钟内一个进程可以并发地开始一个 shell 脚本的 n 个拷贝的次数，n 一般取值 1，2，4，8。这个脚本对一个数据文件进行一系列的变形操作。

**System Call Overhead**

测试进入和离开操作系统内核的代价，即一次系统调用的代价。它利用一个反复地调用 getpid 函数的小程序达到此目的。

**Graphical Tests**

提供2D和3D图形性能测试，但是目前对3D性能测试还非常有限，并且测试的结果只是大致的。测试的结果不仅取决于硬件，还取决于您的系统是否安装了适当的驱动程序。