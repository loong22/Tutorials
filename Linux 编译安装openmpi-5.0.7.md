### 安装zlib
```
wget https://zlib.net/zlib-1.3.1.tar.gz

tar -xzf zlib-1.3.1.tar.gz

cd zlib-1.3.1/

mkdir build && cd build

../configure --prefix=/opt/zlib-1.3.1

make

sudo make install
```

### 安装openmpi
```
wget https://download.open-mpi.org/release/open-mpi/v5.0/openmpi-5.0.7.tar.gz

tar -xzf openmpi-5.0.7.tar.gz

cd openmpi-5.0.7/

mkdir build && cd build

../configure --prefix=/opt/openmpi-5.0.4 --with-cuda=/usr/local/cuda --with-gdrcopy=/usr --with-zlib=/opt/zlib-1.3.1

make -j$(nproc)

sudo make install
```