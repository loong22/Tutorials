### 1. 备份

```
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

### 2. 阿里源

```
sudo vim /etc/apt/sources.list

#将source.list文件内容替换成下面的(阿里源)
deb http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
```

### 3. 清华源

https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse

# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

deb http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
# deb-src http://security.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# # deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

### 4. 更新

```
sudo apt-get update
```

### 5. pip 清华源


```
pip3 install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
```

默认情况下 pip 使用的是国外的镜像，在下载的时候速度非常慢，本文我们介绍使用国内清华大学的源，地址为：

https://pypi.tuna.tsinghua.edu.cn/simple

我们可以直接在 pip 命令中使用 -i 参数来指定镜像地址，例如：
```
pip3 install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
```

以上命令使用清华镜像源安装 numpy 包。

#### 5.1 设为默认
升级 pip 到最新的版本后进行配置：
```
python -m pip install --upgrade pip
pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```
如果您到 pip 默认源的网络连接较差，临时使用本镜像站来升级
```
python -m pip install -i https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple --upgrade pip
```

如果需要全局修改，则需要修改配置文件。

Linux/Mac os 环境中，配置文件位置在 ~/.pip/pip.conf（如果不存在创建该目录和文件）：
mkdir ~/.pip
打开配置文件 ~/.pip/pip.conf，修改如下：
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```
查看 镜像地址：
```
pip3 config list   
```
输出:
```
global.index-url='https://pypi.tuna.tsinghua.edu.cn/simple'
install.trusted-host='https://pypi.tuna.tsinghua.edu.cn'
```

可以看到已经成功修改了镜像。

Windows下，你需要在当前对用户目录下（C:\Users\xx\pip，xx 表示当前使用对用户，比如张三）创建一个 pip.ini在pip.ini文件中输入以下内容：
```
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = pypi.tuna.tsinghua.edu.cn
```

其他国内镜像源
清华大学TUNA镜像源： https://pypi.tuna.tsinghua.edu.cn/simple
阿里云镜像源： http://mirrors.aliyun.com/pypi/simple/
中国科学技术大学镜像源： https://mirrors.ustc.edu.cn/pypi/simple/
华为云镜像源： https://repo.huaweicloud.com/repository/pypi/simple/
腾讯云镜像源：https://mirrors.cloud.tencent.com/pypi/simple/