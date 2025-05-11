
```
在Ubuntu 20.04.3 LTS测试通过
```

1 下载Zlib和OpenSSH源码
------------------

Zlib下载地址

[https://www.zlib.net/fossils/](https://www.zlib.net/fossils/)

OpenSSH下载地址(存在漏洞的版本号为 < 4.4p1, 8.5p1 <= 版本 < 9.8p1 请选择此范围外的版本进行下载安装)

[https://cloudflare.cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/](https://cloudflare.cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/)

2 安装Zlib
--------

```sh
wget https://www.zlib.net/fossils/zlib-1.3.1.tar.gz
tar zxvf zlib-1.3.1.tar.gz
cd zlib-1.3.1
#创建编译目录
mkdir build && cd build
#默认配置选项
../configure
make
make install
```

3 安装OpenSSH
-----------

```sh
wget https://cloudflare.cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.8p1.tar.gz
tar zxvf openssh-9.8p1.tar.gz
cd openssh-9.8p1
#创建编译目录
mkdir build && cd build
#默认配置选项
../configure
#如果报错libcrypto not found.
#解决办法
#sudo apt update && sudo apt install libssl-dev

make

#停止SSH服务
service ssh stop

#安装
make install

#启动SSH服务
service ssh start
```