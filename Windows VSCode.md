
### 1. VScode 安装

VScode 官网地址：[https://code.visualstudio.com/](https://code.visualstudio.com/) VScode 官方文档地址：[https://code.visualstudio.com/docs](https://code.visualstudio.com/docs)

## 2. 安装插件

拓展下载
```
Chinese(中文插件包)
c/c++
Code Runner
CMake Tools
```

安装MSYS2(或者Cygwin也可以)
https://www.msys2.org/

https://www.cygwin.com/

```
#msys2在线安装命令
pacman -Syu
pacman -Su
pacman -S --needed base-devel mingw-w64-x86_64-toolchain
```

### 3. 测试
打开VSCode，创建或者打开一个空白的文件夹

点击新建文件hello.cpp 复制以下内容

```c++
#include <iostream>
#include <vector>
#include <string>
using namespace std;
int main()
{
    vector<string> msg {"Hello", "C++", "World", "from", "VS Code", "and the C++ extension!"};
 for (const string& word : msg)
 {
        cout << word << " ";
 }
    cout << endl;
}
```

在菜单栏，依次点击终端→配置默认生成任务，在弹出的选择框中选择g++.exe build active file，将会自动创建.vscode文件夹和tasks.json文件，如下所示

```JSON
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: g++.exe 生成活动文件",
            "command": "C:\\MinGW\\mingw64\\bin\\g++.exe",
            "args": [
                "-fdiagnostics-color=always",
                "-g",
                "${file}",
                "-o",
                "${fileDirname}\\${fileBasenameNoExtension}.exe"
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
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

回到hello.cpp文件，在菜单栏依次点击 终端→运行生成任务，或者使用快捷键Ctrl+Shift+B；

编译完成后，在源代码下方弹出的终端窗口中，会显示编译是否成功的信息；

在终端窗口右侧点击+按钮，在新的终端窗口中输入dir，可以看到helloworld文件夹中生成了helloworld.exe文件；

输入.\\helloworld.exe执行文件，窗口中显示出程序运行的结果；

调试 回到helloworld.cpp文件，在第10行添加断点

### 方法1
1.点击右上角调试按钮，选择“调试c/c++文件”

2.进入调试，程序执行至断点位置
    

### 方法2

1.依次点击菜单栏 运行→添加配置，选择C++（GDB/LLDB）；
2.在添加配置中，选择(gdb) 启动；
3.VSCode将会自动生成launch.json文件，修改相关程序名和路径；

```JSON
{
    // 使用 IntelliSense 了解相关属性。 
    // 悬停以查看现有属性的描述。
    // 欲了解更多信息，请访问: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) 启动",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/helloWorld.exe",
            "args": [],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "miDebuggerPath": "C:\\MinGW\\mingw64\\bin\\gdb.exe",
            "setupCommands": [
                {
                    "description": "为 gdb 启用整齐打印",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "将反汇编风格设置为 Intel",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ]
        }

    ]
}
```

粘性系数测试

```C++
#include <cmath>  
#include <ostream>
#include <iostream>

double calculateViscosity(double T) {  
    double T0 = 273.16;    // 参考温度   
    double mu0 = 1.7161E-5;       // 在参考温度下的粘性系数  
    double Ts = 124.0;      // 特定的转换温度或与材料属性有关的温度 
    double mu = mu0 * pow(T / T0, 1.5) * ((T0 + Ts) / (T + Ts));  
    return mu;  
}  

double calculateK(double T) {  
    double T0 = 273.16;    // 参考温度   
    double k0 = 0.02415;       // 在参考温度下的热传导系数  
    double Ts = 194.0;      // 特定的转换温度或与材料属性有关的温度 
    double k = k0 * pow(T / T0, 1.5) * ((T0 + Ts) / (T + Ts));  
    return k;  
}  
  
int main() {  
    // 示例参数，你可以根据实际情况修改这些值  
    double T = 300.0;      // 当前温度  

    // 计算粘性系数  
    double mu = calculateViscosity(T);  

    // 输出结果  
    std::cout << "At temperature " << T << "K, the viscosity is " 
        << mu << " pa.s "<< std::endl;  
    

    // 计算热传导系数
    double k = calculateK(T);
    // 输出结果  
    std::cout << "At temperature " << T << "K, the thermal conductivity coefficient is " 
        << k <<" W/(m.K)"<< std::endl; 

        
    return 0;  
}
```