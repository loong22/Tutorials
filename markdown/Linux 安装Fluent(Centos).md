
### 1. 使用VMware安装Centos7
```bash
#64系统 下载地址:https://ftp.riken.jp/Linux/centos/7/isos/x86_64/CentOS-7-x86_64-Everything-2207-02.iso

#安装时选择最全的软件包,不然会缺东西很麻烦
```

### 2. 安装Python3.8
安装
```bash
tar xvf Python-3.8.18.tar.xz
cd Python-3.8.18/
mkdir build
cd build/
../configure --prefix=/home/fluent/bin
make -j 8
make install
```

环境变量
```bash
vim ~/.bashrc
#在最后面添加# User specific aliases and functionsexport PATH=/home/fluent/bin/bin:/usr/lib64/qt-3.3/bin:/home/fluent/perl5/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/fluent/.local/bin
export LD_LIBRARY_PATH=/home/fluent/bin/lib:/usr/local/lib64:/lib64
source ~/.bashrc
```

### 3. 安装Pyfluent的Python库
```bash
#升级pip3, -i后面是镜像站地址,目前使用清华镜像pip3 install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install ansys-fluent-core -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install ansys-fluent-parametric -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install ansys-fluent-visualization -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### 4. 安装fluent
步骤
```bash
#上传ANSYS.2023.R1.Product.Linux64.iso 到用户目录下(可以新建一个文件夹)#创建挂载目录sudo mkdir /mnt/ansys
#挂载sudo mount /home/fluent/ansys/ANSYS.2023.R1.Product.Linux64.iso /mnt/ansys
#安装(GUI界面打开/或者使用X转发)cd /mnt/ansys
./INSTALL#进入GUI界面*******************安装证书管理器先点击Install Ansys License Manager
License Manager Shutdown界面点击OK
安装界面点击同意 -> Next
Install Directory 选择/home/fluent/ansys -> Next -> Next -> Next
现在已经开始安装证书管理器, 安装完成 -> Next
不勾选Launch License...选项, continue, Exit
#返回GUI界面*******************安装主程序点击Install Ansys Products
I agree -> Next ->Install Directory 选择/home/fluent/ansys -> Next -> hostname填入localhost -> Next -> 选择需要的组件(建议全选Ansys,Inc.Products) -> Next -> Next
开始进行安装安装完成 -> Next -> 不勾选Launch ...选项 -> Exit
#返回GUI界面Exit
```

破解
```bash
#没有ld-lsb链接库会导致证书管理器提取不到系统的hostid#如果没有ld-lsb-x86-64.so.3 就执行下面这个命令, 有的话就不要执行sudo cp /lib64/ld-linux-x86-64.so.2  /lib64/ld-lsb-x86-64.so.3
#复制破解文件到安装目录cp -r crack/ansys_inc /home/fluent/ansys/
cd /home/fluent/ansys/ansys_inc/shared_files/licensing/tools/bin/linx64
./ansyslmcenter#进入浏览器界面1.点击 Get System Hostid Information
复制HOSTID, 替换/home/fluent/ansys/ansys_inc/license.txt 中的*******2.点击Add a License File
选择/home/fluent/ansys/ansys_inc/license.txt -> INSTALL LICENSE FILE
3.点击View Status/Start/Stop License Manager
点击Start启动服务,三个服务都是Running就OK
```

测试
```bash
python3 naca0012-single-CGNS-3d.py
#报错如下ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.0.2k-fips  26 Jan 2017'. See: https://github.com/urllib3/urllib3/issues/2168
#安装openssl-1.1.1#切换root用户wget https://www.openssl.org/source/openssl-1.1.1d.tar.gztar xf openssl-1.1.1d.tar.gz
cd openssl-1.1.1d
./configmake && make install
#配置echo "/usr/local/lib64/" >> /etc/ld.so.conf
ldconfigmv /usr/bin/openssl /usr/bin/openssl.old
ln -sv /usr/local/bin/openssl /usr/bin/openssl
#验证openssl version
#重新编译Pythoncd Python-3.8.18/
mkdir build
cd build/
make clean
sudo yum remove openssl-devel
export OPENSSL_LIBS=/usr/local/lib64/libssl.so
../configure  --with-openssl=/usr --with-openssl-rpath=/usr/lib64
make -j 8
make install
#运行报错ImportError: urllib3 v2.0 only supports OpenSSL 1.1.1+, currently the 'ssl' module is compiled with 'OpenSSL 1.0.2k-fips  26 Jan 2017'. See: https://github.com/urllib3/urllib3/issues/2168
#解决pip install urllib3==1.26.6 -i https://pypi.tuna.tsinghua.edu.cn/simple
#报错2:Traceback (most recent call last):  File "naca0012-single-CGNS-3d.py", line 23, in <module>    solver = pyfluent.launch_fluent(version="3d",precision="double", processor_count=1, show_gui=False, mode="solver")  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 667, in launch_fluent
    launch_string = _generate_launch_string(  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 412, in _generate_launch_string
    exe_path = str(get_fluent_exe_path(**argvals))  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 133, in get_fluent_exe_path
    ansys_version = get_ansys_version()  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 96, in get_ansys_version
    raise RuntimeError("No ANSYS version can be found.")RuntimeError: No ANSYS version can be found.
#解决export PYFLUENT_FLUENT_ROOT=/home/fluent/ansys/ansys_2023r1/ansys_inc/v231/fluent/
#报错[fluent@192 case]$ python3 naca0012-single-CGNS-3d.py
pyfluent.launcher ERROR: Exception caught - RuntimeError: The launch process has been timed out.
Traceback (most recent call last):  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 710, in launch_fluent
    raise ex
  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 694, in launch_fluent
    _await_fluent_launch(  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 356, in _await_fluent_launch
    raise RuntimeError("The launch process has been timed out.")RuntimeError: The launch process has been timed out.
The above exception was the direct cause of the following exception:
Traceback (most recent call last):  File "naca0012-single-CGNS-3d.py", line 23, in <module>    solver = pyfluent.launch_fluent(version="3d",precision="double", processor_count=1, show_gui=False, mode="solver")  File "/home/fluent/bin/lib/python3.8/site-packages/ansys/fluent/core/launcher/launcher.py", line 746, in launch_fluent
    raise LaunchFluentError(launch_cmd) from ex
ansys.fluent.core.launcher.launcher.LaunchFluentError:
Fluent Launch string: /home/fluent/ansys/ansys_inc/v231/fluent/bin/fluent 3ddp   -sifile="/tmp/serverinfo-0rz5ew9g.txt" -nm -hidden
```

安装Intel MPI
```bash
chmod +x l_mpi_oneapi_p_2021.10.0.49374_offline.sh
./l_mpi_oneapi_p_2021.10.0.49374_offline.sh#Installation location: /home/fluent/intel/oneapisource setvars.sh
```

开机执行操作
```bash
/home/fluent/ansys/ansys_inc/shared_files/licensing/tools/bin/linx64/ansyslmcenter/home/fluent/ansys/ansys_inc/shared_files/licensing/tools/bin/linx64/ansyslmcenter先开启证书管理器, 三个服务都是running就ok
```