
# Mutation++

## 飞书文档


### [Windwos平台环境搭建](https://y7ifiegl99.feishu.cn/wiki/HM60wQ948i7JRSkgNGScIULEn3e)


### [Windows平台编译](https://y7ifiegl99.feishu.cn/wiki/NK3wwg4k8iMJgQk3oSmcq2oXnFc)



# [Visual Studio Code 配置C、C++ 文件debug调试环境](https://www.cnblogs.com/elmluo/p/15933484.html)


# VScode调试

## 安装vscode(Windows 或者 Linux)

### 1. 通过密钥方式连接远程SSH实现远程调试，配置文件如下：
```config
Host 10.10.12.30
      HostName 10.10.12.30
      User ubuntu
      Port 22
      PasswordAuthentication no
      IdentityFIle C:\Users\Admin\.ssh\ubuntu_key
```
配置文件位置为：C:\Users\Admin\.ssh\config

### 2. 在远程服务器上安装拓展：C/C++，CMake，CMake tools， CodeLLDB， C/C++Runner，C/C++ Extension Pack， Remote SSH， Chinese中文包。

### 3. 下载源代码
通过ssh连接远程后在用户目录/或指定目录下下载源代码
```sh
git clone https://github.com/mutationpp/Mutationpp.git
```

### 4. 使用VScode打开文件夹为Mutationpp的源码路径

### 5. 选择Cmake编译包为GCC，模式为Debug， 然后运行Build

### 6. 选择运行->添加配置(添加Debug配置)
配置文件.vscode/launch.json如下
```json
{
    "version": "0.2.0",
    "configurations": [

      {
        "name": "C/C++ Runner: Debug Session",
        "type": "cppdbg",
        "request": "launch",
        "args": ["air_5"],
        "stopAtEntry": false,
        "externalConsole": false,
        "cwd": "/home/ubuntu/dev/Mutationpp/build",
        "program": "/home/ubuntu/dev/Mutationpp/build/src/apps/checkmix",
        "MIMode": "gdb",
        "miDebuggerPath": "gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing -gdb-set disassembly-flavor intel",
            "ignoreFailures": true
          }
        ]
      }
    ]
}

```

另一份参考配置(在本地虚拟机上面需要这么配置，不知道为啥)
```json
{
    "version": "0.2.0",
    "configurations": [

      {
        "name": "C/C++ Runner: Debug Session",
        "type": "cppdbg",
        "request": "launch",
        "args": ["air_5"],
        "stopAtEntry": false,
        "externalConsole": false,
        "cwd": "${workspaceFolder}/build/src/apps",
        "program": "${workspaceFolder}/build/src/apps/checkmix",
        "MIMode": "gdb",
        "miDebuggerPath": "gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing -gdb-set disassembly-flavor intel",
            "ignoreFailures": true
          }
        ]
      }
    ]
}
```

调试SU2时候使用的配置，可以参考
```json
{
    "version": "0.2.0",
    "configurations": [

      {
        "name": "C/C++ Runner: Debug Session",
        "type": "cppdbg",
        "request": "launch",
        "args": ["inv_NACA0012.cfg"],
        "stopAtEntry": false,
        "externalConsole": false,
        "cwd": "/home/cfd/src/SU2-master/build/SU2_CFD/src",
        "program": "/home/cfd/src/SU2-master/build/SU2_CFD/src/SU2_CFD",
        "MIMode": "gdb",
        "miDebuggerPath": "gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing -gdb-set disassembly-flavor intel",
            "ignoreFailures": true
          }
        ]
      }
    ]
}

```



### 7. 在src/apps/checkmix.cpp里面添加断点，点击运行->启动调试，就可以开始调试了。

### 8. 调试mppshock.cpp，在src/apps/mppshock.cpp里面添加断点，修改配置文件如下, 点击运行->启动调试，就可以开始调试了。
```
{
    "version": "0.2.0",
    "configurations": [


      {
        "name": "C/C++ Runner: Debug Session",
        "type": "cppdbg",
        "request": "launch",
        "args": [" -P 1000", "-V 10000", "-T 300", "-m 1", "air_5"],
        "stopAtEntry": false,
        "externalConsole": false,
        "cwd": "/home/ubuntu/dev/Mutationpp/build",
        "program": "/home/ubuntu/dev/Mutationpp/build/src/apps/mppshock",
        "MIMode": "gdb",
        "miDebuggerPath": "gdb",
        "setupCommands": [
          {
            "description": "Enable pretty-printing for gdb",
            "text": "-enable-pretty-printing -gdb-set disassembly-flavor intel",
            "ignoreFailures": true
          }
        ]
      }
    ]
}

```



# 拓展（调试Cpp单文件的配置文件)

## 1. 编写main.cpp
```cpp
#include <iostream>
#include <vector>
#include <string>
using namespace std;
int main()
{
    vector<string> msg {"Hello", "C++", "World", "from", "VS Code", "and the C++ extension!"
};
 for (const string& word : msg)
 {
        cout << word << " ";
 }
    cout << endl;
}
```

## 2. 点击终端->配置默认生成任务
编辑.vscode/task.json, 示例配置如下
```json
{
        "version": "2.0.0",
        "tasks": [
                {
                        "type": "cppbuild",
                        "label": "C/C++: g++ 生成活动文件",
                        "command": "/usr/bin/g++",
                        "args": [
                                "-fdiagnostics-color=always",
                                "-g",
                                "${file}",
                                "-o",
                                "${fileDirname}/${fileBasenameNoExtension}"
                        ],
                        "options": {
                                "cwd": "${fileDirname}"
                        },
                        "problemMatcher": [
                                "$gcc"
                        ],
                        "group": {
                                "kind": "build",
                                "isDefault": true
                        },
                        "detail": "编译器: /usr/bin/g++"
                }
        ]
}
```
然后运行Build，构建main执行程序

## 3. 点击运行->添加配置
编写.vscode/launch.json, 示例配置如下
```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "C/C++ Runner: Debug Session",
      "type": "cppdbg",
      "request": "launch",
      "args": ["air_5"],
      "stopAtEntry": false,
      "externalConsole": false,
      "cwd": "/home/ubuntu/src/Mutationpp/build_debug",
      "program": "/home/ubuntu/src/Mutationpp/build_debug/main",
      "MIMode": "gdb",
      "miDebuggerPath": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing -gdb-set disassembly-flavor intel",
          "ignoreFailures": true
        }
      ]
    }
  ]
}
```

## 4. 在main.cpp文件中添加断点，点击运行->启动调试, 即可调试程序


























```
测试
```

# Linux编译安装(OK)

[https://github.com/mutationpp/Mutationpp/blob/master/docs/installation.md#top](https://github.com/mutationpp/Mutationpp/blob/master/docs/installation.md#top)

```
按照上面的教程操作，Ubuntu22.04系统无报错，可正常运行测试程序
#运行ctest时第17项测试失败，还在排查问题
```

报错如下:

```
      Start 42: run_tests:Thermal diffusion ratios sum to zero
42/58 Test #42: run_tests:Thermal diffusion ratios sum to zero ..........................   Passed    1.48 sec
      Start 43: run_tests:SpeciesListDescriptor tests
43/58 Test #43: run_tests:SpeciesListDescriptor tests ...................................   Passed    0.01 sec
      Start 44: run_tests:Loading NASA-7 thermodynamic database
44/58 Test #44: run_tests:Loading NASA-7 thermodynamic database .........................   Passed    0.08 sec
      Start 45: run_tests:Loading NASA-9 thermodynamic database
45/58 Test #45: run_tests:Loading NASA-9 thermodynamic database .........................   Passed    0.08 sec
      Start 46: run_tests:Loading NASA-9-New thermodynamic database
46/58 Test #46: run_tests:Loading NASA-9-New thermodynamic database .....................   Passed    0.03 sec
      Start 47: run_tests:Loading RRHO thermodynamic database
47/58 Test #47: run_tests:Loading RRHO thermodynamic database ...........................   Passed    0.02 sec
      Start 48: run_tests:Energy transfer source terms are zero in equilibrium
48/58 Test #48: run_tests:Energy transfer source terms are zero in equilibrium ..........   Passed    3.98 sec
      Start 49: run_tests:Units
49/58 Test #49: run_tests:Units .........................................................   Passed    0.01 sec
      Start 50: run_tests:Utility functions
50/58 Test #50: run_tests:Utility functions .............................................   Passed    0.00 sec
      Start 51: run_tests:XML classes
51/58 Test #51: run_tests:XML classes ...................................................   Passed    0.00 sec
      Start 52: run_tests:Species production rates sum to zero
52/58 Test #52: run_tests:Species production rates sum to zero ..........................   Passed    1.04 sec
      Start 53: run_tests:Species production rates are zero in equilibrium
53/58 Test #53: run_tests:Species production rates are zero in equilibrium ..............   Passed    0.57 sec
      Start 54: bugfix_120
54/58 Test #54: bugfix_120 ..............................................................   Passed    0.02 sec
      Start 55: example_O2_dissociation
55/58 Test #55: example_O2_dissociation .................................................   Passed    3.92 sec
      Start 56: example_air_diffusion_comparison
56/58 Test #56: example_air_diffusion_comparison ........................................   Passed    0.05 sec
      Start 57: example_equilibrium_air
57/58 Test #57: example_equilibrium_air .................................................   Passed    0.04 sec
      Start 58: example_tutorial
58/58 Test #58: example_tutorial ........................................................   Passed    0.08 sec

98% tests passed, 1 tests failed out of 58

Label Time Summary:
bugs               =   0.12 sec*proc (2 tests)
comparisons        =   0.01 sec*proc (1 test)
diffusionmatrix    =   0.77 sec*proc (1 test)
equilibrium        =   0.70 sec*proc (1 test)
exceptions         =   0.01 sec*proc (1 test)
gsi                =   0.56 sec*proc (3 tests)
kinetics           =   3.98 sec*proc (6 tests)
loading            =   1.24 sec*proc (11 tests)
mixtures           =   1.03 sec*proc (6 tests)
run_tests          =  16.47 sec*proc (53 tests)
thermo             =   0.02 sec*proc (3 tests)
thermodynamic      =   0.63 sec*proc (1 test)
thermodynamics     =   2.36 sec*proc (13 tests)
transfer           =   4.09 sec*proc (4 tests)
transport          =   3.77 sec*proc (12 tests)
utilities          =   0.02 sec*proc (3 tests)

Total Test time (real) =  20.63 sec

The following tests FAILED:
     17 - run_tests:Comparison tests (Failed)
Errors while running CTest
Output from these tests are in: /home/cfd/src/Mutationpp/build/Testing/Temporary/LastTest.log
Use "--rerun-failed --output-on-failure" to re-run the failed cases verbosely.
```

进入build路径下的tests文件,运行run\_tests输出如下:

```
/home/cfd/src/Mutationpp/tests/c++/test_reaction_rates.cpp:54: FAILED:
  CHECK( rate.getLnRate(std::log(T), 1.0/T) == Approx(std::log(k)) )
with expansion:
  25.6485685898 == Approx( 27.0429011177 )

/home/cfd/src/Mutationpp/tests/c++/test_reaction_rates.cpp:56: FAILED:
  CHECK( rate.derivative(k, std::log(T), 1.0/T) == Approx(k * (n + theta / T) / T) )
with expansion:
  24353970.8534214981
  ==
  Approx( -61687253.0848957524 )

/home/cfd/src/Mutationpp/tests/c++/test_reaction_rates.cpp:54: FAILED:
  CHECK( rate.getLnRate(std::log(T), 1.0/T) == Approx(std::log(k)) )
with expansion:
  25.6826746603 == Approx( 26.9375739354 )

/home/cfd/src/Mutationpp/tests/c++/test_reaction_rates.cpp:56: FAILED:
  CHECK( rate.derivative(k, std::log(T), 1.0/T) == Approx(k * (n + theta / T) / T) )
with expansion:
  12756136.3385219779
  ==
  Approx( -49970006.7491000891 )

Warning: the NASA-9-New thermodynamic database is assembled from a collection of sources and is still being vetted. See Scoggins et al. Aerospace Science and Technology 66:177-192, 2017. for more details.  The name of this database may change in the future.
Warning: the NASA-9-New thermodynamic database is assembled from a collection of sources and is still being vetted. See Scoggins et al. Aerospace Science and Technology 66:177-192, 2017. for more details.  The name of this database may change in the future.
===============================================================================
test cases:    53 |    50 passed |   3 failed
assertions: 52247 | 52035 passed | 212 failed
```

总结：测试程序可以正常运行

# Windows编译Cmake+VS2019安装(失败)

Cmake勾选选项如下: !\[\[1720597546112.png\]\]

```
Windows平台需要修改源码才能正确编译成功（V1.0.5）
git clone https://github.com/mutationpp/Mutationpp.git
```

修改代码示例如下: !\[\[1720597494359.png\]\]

运行示例代码报错:

```
M++ error: invalid input.
input: key = RRHO
Provider <RRHO> for type ThermoDB is not registered.  Possible providers are:

Was trying to load the thermodynamic database.


D:\Build\Mutationpp-1.0.5\examples\c++\O2_dissociation\Debug\O2_dissociation.exe (进程 17968)已退出，代码为 1。
按任意键关闭此窗口. . .
```

!\[\[1720597651605.png\]\]

目前已经定位到问题是mutation++生成lib库/dll库存在问题，~还在排查Cmakelist文件~（PS：放弃这个方案） （把测试app的代码放到mutaion++并修改生成目标为exe可执行程序，这样就可以正常运行测试app)

* * *

# Windows改用Cygwin编译(OK)

#### 1. 下载安装Cygwin

```
#下载地址
wget https://www.cygwin.com/setup-x86_64.exe
```

安装组件选择如下： !\[\[Pasted image 20240711090233.png\]\]

#### 2.把mutation++源码放在用户文件夹内

```
rar x Mutationpp-1.0.5(Modified).rar

cd Mutationpp

mkdir build

cd build
```

#### 3.ccmake编译

```
ccmake ../
```

编译选项如下： !\[\[Pasted image 20240711092531.png\]\]

#### 4.运行ctest报错

!\[\[Pasted image 20240711092723.png\]\] 忽略。。。

#### 5.编译测试案例

添加环境变量

```

#可执行程序路径
export PATH=$PATH:/home/Loong/bin/mutation++/bin

#C头文件
export C_INCLUDE_PATH=$C_INCLUDE_PATH:/home/Loong/bin/mutation++/include/mutation++
:/home/Loong/Mutationpp/thirdparty/eigen:/home/Loong/Mutationpp/thirdparty/catch

#C++头文件
export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH:/home/Loong/bin/mutation++/include/mutation++:/home/Loong/Mutationpp/thirdparty/eigen:/home/Loong/Mutationpp/thirdparty/catch

#编译静态库
export LIBRARY_PATH=$LIBRARY_PATH:/home/Loong/bin/mutation++/lib

#动态运行库
export LD_INCLUDE_PATH=$LD_INCLUDE_PATH:/home/Loong/bin/mutation++/lib:/home/Loong/bin/mutation++/bin

```

编译示例案例

```
#复制编译完成的静态库名(只执行一次)
cp /home/Loong/bin/mutation++/lib/libmutation++.dll.a /home/Loong/bin/mutation++/lib/libmutation++.a


#进入案例文件夹
cd Mutationpp/examples/c++/O2_dissociation/

#编译
g++ O2_dissociation.cpp -o O2_dissociation.cpp.exe -lmutation++

#执行
./O2_dissociation.cpp.exe
```

输出如下(与官方示例输出一致)： !\[\[Pasted image 20240711093853.png\]\]

如果需要在cygwin窗口外执行生成的exe程序 方案一：添加cygwin和mutation++的环境变量到系统路径下 此电脑->属性->高级属性设置->高级->环境变量->系统变量->Path

```
#添加路径如下
添加路径C:\cygwin64\bin
添加路径C:\cygwin64\home\Loong\bin\mutation++\bin
```

方案二：复制dll文件到生成的exe文件夹内

```
C:\cygwin64\bin
路径下的cygwin1.dll, cygstdc++-6.dll, cyggcc_s-seh-1.dll

C:\cygwin64\home\Loong\bin\mutation++\bin
路径下的cygmutation++.dll
```












