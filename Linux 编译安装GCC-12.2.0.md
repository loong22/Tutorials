1. **检查当前版本**：
   - 检查 `make` 版本：`make -v`
   - 检查 `gcc` 版本：`gcc -v`

2. **升级 `make`**：
   - 下载最新版本：
     ```bash
     wget https://ftp.gnu.org/pub/gnu/make/make-4.4.tar.gz
     ```
   - 解压并安装：
     ```bash
     tar -zxf make-4.4.tar.gz
     cd make-4.4
     ./configure --prefix=/usr
     make
     make install
     ```
   - 验证版本：`make -v`

3. **升级 `gcc`**：
   - 下载最新版本：
     ```bash
	 sudo yum install gcc
	 sudo yum install gcc-c++
	 
     wget https://ftp.gnu.org/gnu/gcc/gcc-12.2.0/gcc-12.2.0.tar.gz
     ```
   - 解压并安装：
     ```bash
     tar -zxf gcc-12.2.0.tar.gz
     cd gcc-12.2.0
     ./contrib/download_prerequisites
     mkdir build
     cd build
     ../configure --enable-checking=release --enable-languages=c,c++ --disable-multilib  --prefix=/opt/gcc-12.2.0
     make -j$(nproc)
     make install
     ```
   - 验证版本：`gcc -v`

4. **重新尝试编译 glibc 2.28**：
   - 确保使用正确的配置命令：
     ```bash
     ../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin
     make -j$(nproc)
     make install
     ```
     如果报错-Werror=array-parameter=, 可以选择 禁用 -Werror
     ```
     mkdir build && cd build
     ../configure --prefix=/usr --disable-werror
     make
     ```
