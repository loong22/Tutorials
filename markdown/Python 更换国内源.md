
# 1. 本次指定源

```bash
#升级pip3, -i后面是镜像站地址,目前使用清华镜像pip3 install --upgrade pip -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install ansys-fluent-core -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install ansys-fluent-parametric -i https://pypi.tuna.tsinghua.edu.cn/simple
pip3 install ansys-fluent-visualization -i https://pypi.tuna.tsinghua.edu.cn/simple
```

# 2. 更新系统pip源

```bash
# LinuxLinux/Mac os 环境中，配置文件位置在 ~/.pip/pip.conf（如果不存在创建该目录和文件）：
mkdir ~/.pip
打开配置文件 ~/.pip/pip.conf，修改如下：[global]index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]trusted-host = https://pypi.tuna.tsinghua.edu.cn
查看 镜像地址：
$ pip3 config list
global.index-url='https://pypi.tuna.tsinghua.edu.cn/simple'install.trusted-host='https://pypi.tuna.tsinghua.edu.cn'可以看到已经成功修改了镜像。# WindowsWindows下，你需要在当前对用户目录下（C:\Users\xx\pip，xx 表示当前使用对用户，比如张三）创建一个 pip.ini在pip.ini文件中输入以下内容：
[global]index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]trusted-host = pypi.tuna.tsinghua.edu.cn
# 其他国内镜像源- 中国科学技术大学 : https://pypi.mirrors.ustc.edu.cn/simple
- 豆瓣：http://pypi.douban.com/simple/
- 阿里云：http://mirrors.aliyun.com/pypi/simple/
```