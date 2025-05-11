
### 1. 安装支持工具
```

sudo apt-get update

sudo apt-get install build-essential

sudo apt-get install cmake flex bison
```

### 2. 安装最新版本Doxygen
```
git clone https://github.com/doxygen/doxygen.git

cd doxygen

mkdir build

cd build

cmake -G "Unix Makefiles" ../

make

sudo make install
```

参考:

[https://blog.csdn.net/happycrcr/article/details/124914965](https://blog.csdn.net/happycrcr/article/details/124914965)