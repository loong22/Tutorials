### 1. 下载源码
```
wget https://www.python.org/ftp/python/3.11.0/Python-3.11.0.tgz

tar xvf Python-3.11.0.tgz

cd Python-3.11.0.tgz

mkdir build && cd build
```

### 2. 安装必要的依赖
```
sudo apt update

sudo apt install build-essential libssl-dev zlib1g-dev libncurses-dev libreadline-dev libsqlite3-dev libgdbm-dev libdb-dev libbz2-dev libexpat1-dev liblzma-dev tk-dev libffi-dev uuid-dev libnsl-dev libgmp-dev
```

### 3. 编译安装
```
../configure --enable-optimizations

make -j$(nproc)

sudo make install
```