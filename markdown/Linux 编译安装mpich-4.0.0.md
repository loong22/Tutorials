
### 1. 下载相关依赖包
官方离线包下载网站
[https://www.mpich.org/static/downloads/](https://www.mpich.org/static/downloads/)

```bash
#示例下载
wget https://www.mpich.org/static/downloads/4.0/mpich-4.0.tar.gz
```

### 2. 安装
```bash
tar zxvf mpich-4.0.tar.gz && cd mpich-4.0
mkdir build && cd build
chmod +x ../configure && ../configure --prefix=/opt/mpich-4.0.1 --with-gmp=/opt/gcc-9.5.0/gmp-6.1.0 --with-mpfr=/opt/gcc-9.5.0/mpfr-3.1.4 --with-mpc=/opt/gcc-9.5.0/mpc-1.0.3 --with-gcc=/opt/gcc-9.5.0
make -j 8 && make install
```