# ParaViewWeb
https://www.cnblogs.com/chanyuantiandao/p/15759670.html

https://kitware.github.io/visualizer/

https://www.npmjs.com/package/pvw-visualizer
## Paraview报错找不到xcb解决办法
```
sudo apt update -q
sudo apt install -y -q build-essential libgl1-mesa-dev

sudo apt install -y -q libxkbcommon-x11-0
sudo apt install -y -q libxcb-image0
sudo apt install -y -q libxcb-keysyms1
sudo apt install -y -q libxcb-render-util0
sudo apt install -y -q libxcb-xinerama0
sudo apt install -y -q libxcb-icccm4

```

# PCIE 4.0 5.0
https://www.intel.com/content/www/us/en/gaming/resources/what-is-pcie-4-and-why-does-it-matter.html

# SSH
https://blog.xlab.qianxin.com/li-yong-ssh/


# Paraview网页版运行命令
```
#Paraview版本为5.10.0

#打开渲染服务器
./mpiexec -np 4 ./pvrenderserver

#打开数据存放服务器
./mpiexec -np 4 ./pvdataserver

#打开paraview网页版
./bin/pvpython -m paraview.apps.visualizer --data ./share/paraview-5.10/examples --port 9001 --host 0.0.0.0 --rs-host 127.0.0.1 --rs-port 22221 --ds-host 127.0.0.1 --ds-port 11111

#存在问题
1. 网页版进行切片没有可视化提示(不能拖动鼠标进行切片，只能输入参数)
2. 网页版所有人在同一个端口上看到的东西都是一样的(除非换端口)
3. 网页版进行放大缩小时可能导致模型渲染超出范围，需要点击居中后才能显示模型

```

https://github.com/Kitware/paraview-visualizer


```
noVNC版本
Linux需要安装下面这些依赖
sudo apt-get install '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev

```








