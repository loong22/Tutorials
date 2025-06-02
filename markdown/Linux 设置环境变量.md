### 1. 常用的环境变量
```sh
export PATH=$PATH:/usr/local/tecplot/360ex_2023r1/bin

export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/tecplot/360ex_2023r1/bin/llvm:/usr/local/tecplot/360ex_2023r1/bin/osmesa:/usr/local/tecplot/360ex_2023r1/bin:/usr/local/tecplot/360ex_2023r1/bin/sys-util:/usr/local/tecplot/360ex_2023r1/bin/Qt

export LIBRARY_PATH=$LIBRARY_PATH

export C_INCLUDE_PATH=$C_INCLUDE_PATH

export CPLUS_INCLUDE_PATH=$CPLUS_INCLUDE_PATH

export PYTHONPATH=$PYTHONPATH

```

### 2. 网络代理环境变量
```
#监听本地1080端口
export http_proxy="socks5://127.0.0.1:1080"

export https_proxy="socks5://127.0.0.1:1080"

export ftp_proxy="socks5://127.0.0.1:1080"

export all_proxy="socks5://127.0.0.1:1080"

export no_proxy="localhost,127.0.0.1"
```


