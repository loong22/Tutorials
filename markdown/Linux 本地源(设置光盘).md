# 1. Ubuntu/Debian

```bash
#创建挂载目录
sudo mkdir /mnt/cdrom

#进行挂载
sudo mount -t iso9660 -o loop /dev/sr0 /mnt/cdrom  #or mount -t auto /dev/sr0 /mnt/cdrom

#添加本地目录到软件源
sudo apt-cdrom -m -d=/mnt/cdrom add

#检测apt源
cat /etc/apt/sources.list

#出现下面这个字段表示已经挂载完成
deb cdrom:[Ubuntu 22.04.1 LTS _Jammy Jellyfish_ - Release amd64 (20220809.1)]/ jammy main restricted

#进行本地库依赖更新
apt-get update

#升级软件包
apt-get upgrade

#常用依赖安装
apt-get install build-essential

#对于libc6-i386
#把原来的版本删除后，再进行安装
dpkg -l | grep libc6-i386
dpkg -r libc6-i386
dpkg -l | grep libgo
dpkg -r libgompl

#执行完成后再进行libc-i386安装
apt-get install libc6-i386

#安装完成后再安装build-essential
apt-get install essential

#成功执行后，说明一些必要的依赖都已经安装完成
gcc -v
```

## 1.1 Ubuntu/Debian软件包相关操作

```bash
sudo apt-get update: 升级安装包相关的命令,刷新可安装的软件列表(但是不做任何实际的安装动作)sudo apt-get upgrade: 进行安装包的更新(软件版本的升级)sudo apt-get dist-upgrade: 进行系统版本的升级(Ubuntu版本的升级)sudo do-release-upgrade: Ubuntu官方推荐的系统升级方式,若加参数-d还可以升级到开发版本,但会不稳定
sudo apt-get autoclean: 清理旧版本的软件缓存
sudo apt-get clean: 清理所有软件缓存
sudo apt-get autoremove: 删除系统不再使用的孤立软件
apt（Advanced Packaging Tool）是一个在 Debian 和 Ubuntu 中的 Shell 前端软件包管理器。 apt 命令提供了查找、安装、升级、删除某一个、一组甚至全部软件包的命令，而且命令简洁而又好记。 apt 命令执行需要超级管理员权限(root)。

apt 常用命令
列出所有可更新的软件清单命令：
sudo apt update

升级软件包：
sudo apt upgrade

列出可更新的软件包及版本信息：
apt list --upgradeable

升级软件包，升级前先删除需要更新软件包：
sudo apt full-upgrade

安装指定的软件命令：
sudo apt install <package_name>

安装多个软件包：
sudo apt install <package_1> <package_2> <package_3>

更新指定的软件命令：
sudo apt update <package_name>

显示软件包具体信息,例如：版本号，安装大小，依赖关系等等：
sudo apt show <package_name>

删除软件包命令：
sudo apt remove <package_name>

清理不再使用的依赖和库文件: 
sudo apt autoremove

移除软件包及配置文件: 
sudo apt purge <package_name>

查找软件包命令： 
sudo apt search keyword

列出所有已安装的包：
apt list --installed

列出所有已安装的包的版本信息：
apt list --all-versions

#实例查看一些可更新的包：
sudo apt update

升级安装包：
sudo apt upgrade

#在以上交互式输入字母 Y 即可开始升级，可以将以下两个命令组合起来，一键升级：
sudo apt update && sudo apt upgrade -y

```

## 1.2 Ubuntu查看crontab运行日志

```
#安装日志服务
apt-get install rsyslog
#修改配置文件
vim /etc/rsyslog.d/50-default.conf
#取消 cron 行的注释 重启 系统日志服务
sudo service rsyslog restart

#crontab定时任务

*    *     *     *     *  Run
min  hour data month week Run
```


# 2. Centos/Redhat


```bash
#1. 挂载光盘到指定目录
mount -t iso9660 /dev/cdrom /mnt/cdrom

#2. 挂载作为本地源
cd /etc/yum.repos.d
#如有repo文件，先备份为.bak文件，然后删除repo文件，否则会冲突
cp centos.repo centos.repo && rm centos.repo

#编辑本地源文件local.repovim 
/etc/yum.repos.d/local.repo

#3. 修改为以下内容

[local]
name=cdrom
baseurl=file:///mnt/cdrom
enabled=1
gpgcheck=0
#gpgkey=file:///mnt/cdrom/***

```

## 2.1 RedHat软件包相关操作

```bash
列出所有可更新的软件清单命令：yum check-update
更新所有软件命令：yum update
仅安装指定的软件命令：yum install <package_name>仅更新指定的软件命令：yum update <package_name>列出所有可安裝的软件清单命令：yum list
删除软件包命令：yum remove <package_name>查找软件包命令：yum search keyword
清除缓存命令:yum clean packages: 清除缓存目录下的软件包
yum clean headers: 清除缓存目录下的 headers
yum clean oldheaders: 清除缓存目录下旧的 headers
yum clean, yum clean all (= yum clean packages; yum clean oldheaders) :清除缓存目录下的软件包及旧的 headers
#Centos设置国内源（1）备份/etc/yum.repos.d/CentOS-Base.repomv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup（2）下载对应版本 repo 文件, 放入 /etc/yum.repos.d/ (操作前请做好相应备份)例如：<http://mirrors.163.com/.help/CentOS7-Base-163.repo><https://lug.ustc.edu.cn/wiki/mirrors/help/centos>
```
