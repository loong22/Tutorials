
### 1. 文件操作命令
--------

`ls`
```
功能：显示文件和目录的信息
ls　以默认方式显示当前目录文件列表
ls -a 显示所有文件包括隐藏文件
ls -l 显示文件属性，包括大小，日期，符号连接，是否可读写及是否可执行
ls -lh 显示文件的大小，以容易理解的格式印出文件大小 (例如 1K 234M2G)
ls -lt 显示文件，按照修改时间排序
ls -lhS 按照文件大小进行排序
ls -lh | sort -k 5 -h -r 按照文件大小进行排序
```

`cd`
```
功能：改名目录
cd dir　切换到当前目录下的dir目录
cd /　切换到根目录
cd ..　切换到到上一级目录
cd ../..　切换到上二级目录
cd ~　切换到用户目录，比如是root用户，则切换到/root下
```

`cp`
```
功能：copy文件
cp source target　将文件source复制为target
cp /root /source　将/root下的文件source复制到当前目录
cp –av soure_dir target_dir　将整个目录复制，两目录完全一样
cp /root/{1.txt,2.txt,3.txt} /home/
把同一个目录下多个文件复制到目标文件夹

echo /home/aaronkilik/test/ /home/aaronkilik/tmp | xargs -n 1 cp -v /home/aaronkilik/bin/1.sh
#1.-n 1 - 告诉 xargs 命令每个命令行最多使用一个参数，并发送到 cp 命令中。
#2.cp – 用于复制文件。
#3.-v– 启用详细模式来显示更多复制细节。
把目录下的单个文件复制到多个目标文件夹下

cp file_a/a file_a/b file_a/c file_b
复制多个文件到同一个目标文件夹下
```

`scp`
```
功能：安全copy
例如：
 #scp -r 用户名@IP地址:文件目录 本地目录
scp -r root@172.168.12.40:/root/opt/test.zip /home/documents  # -r可不添加，确认的意思？
scp -P 5400 root@172.168.12.40:/root/opt/test.zip /home/documents  #指定远程的ssh端口
ssh csd@12.34.12.4 -p 5400
```

`rm`
```
功能：删除文件或目录
rm file　删除某一个文件
rm -f file 删除时候不进行提示。可以于r参数配合使用
rm -rf dir　删除当前目录下叫dir的整个目录
rm -rf dir1/  #如果dir1/是一个软连接，那这个命令会把原文件夹内的所有内容都删除掉，而软连接本身不会被删除！需要注意，如果只需要删除软连接，注意文件夹后不要带/。可以使用rm -rf dir1
```

`mv`
```
功能：将文件移动走，或者改名，在uinx下面没有改名的命令，如果想改名，可以使用该命令
mv source target　将文件source更名为target
```

`diff`
```
功能：比较文件内容
diff dir1 dir2　比较目录1与目录2的文件列表是否相同，但不比较文件的实际内容，不同则列出
diff file1 file2　比较文件1与文件2的内容是否相同，如果是文本格式的文件，则将不相同的内容显示，如果是二进制代码则只表示两个文件是不同的
comm file1 file2　比较文件，显示两个文件不相同的内容
```

`ln`
```
功能：建立链接。windows的快捷方式就是根据链接的原理来做的
ln source_path target_path #硬连接 ln 源文件 目标文件
ln -s source_path target_path #软连接
```

`file`
```
功能：查看文件属性，UTF-8、exe，ASCII等
file hellow.c
```

`which`
```
which gcc
```

`chattr & lsattr`
```
例如：
root@ubuntu:/home/barret/work# rm -f 1.md
rm: cannot remove ‘1.md’: Operation not permitted

#这个时候可以通过 lsattr 命令看看该文件是否被打了 flags：
root@ubuntu:/home/barret/work# lsattr 1.md
----i--------e-- ./1.md

#如果文件上存在 `i` 标记，那肯定是删不掉的，同样这个文件也不能被编辑。可以进入 root 模式，去除这个标记：
root@ubuntu:/home/barret/work# chattr -i 1.md
#给保护文件添加标记的方式：
root@ubuntu:/home/barret/work# chattr +i 1.md
```

`Linux运行Windows系统下编写的sh文件出错`
```
:set ff?
#如果出现fileforma＝dos那么就基本可以确定是这个问题
#执行下面这两条命令，就可以执行sh文件了
:set fileformat=unix
:wq
```

`Linux显示不同文件颜色`
```
vim ~/.bashrc
# 加入下列内容，然后执行source ~/.bashrc 生效
export LS_OPTIONS='--color=auto'
eval "`dircolors`"
alias ls='ls $LS_OPTIONS'
alias ll='ls $LS_OPTIONS -l'
alias l='ls $LS_OPTIONS -lA'
```

`cat`
```
# 显示文件的内容，和DOS的type相同
cat file
```

`more`
```
功能：分页显示命令
more　file
more命令也可以通过管道符(|)与其他的命令一起使用,例如：
ps ux|more
ls | more
```

`tail`
```
功能：显示文件的最后几行
tail -n 100 aaa.txt 显示文件aaa.txt文件的最后100行
tail -f example.txt 只要文件有变化就继续输出
```

`vi & vim`
```
vi file　编辑文件file
vi 原基本使用及命令：
输入命令的方式为
先按[ESC]键，然后输入
:w(写入文件),
:w!(不询问方式写入文件）,
:wq保存并退出,
:q退出,
q!不保存退出
:set nu显示行号
:set nonu不显示行号
```

`touch`
```
功能：创建一个空文件
touch aaa.txt 创建一个空文件，文件名为aaa.txt
```

`head`
```
功能：查看文件前几行
head -n 100 aaa.txt 显示aaa.txt文件的最开始的100行
```

`less & more`
```
查看文件内容，less可向上、下翻页，more只能向下翻页。显示一页或几行的内容
```

### 2. 基本系统命令
--------

`Ubuntu修改主机名`
```
#显示当前主机名
hostnamectl

#修改主机名为host.example.com
sudo hostnamectl set-hostname host.example.com sudo hostnamectl set-hostname "Your Pretty HostName" --pretty sudo hostnamectl set-hostname host.example.com --static sudo hostnamectl set-hostname host.example.com --transient

#如果是云实例
#编辑`/etc/cloud/cloud.cfg`文件
#搜索"preserve_hostname”,并且将值从`false`修改到`true`
#示例如下

# This will cause the set+update hostname module to not operate (if true) preserve_hostname: true

#验证
hostnamectl
```

`man`
```
功能：查看某个命令的帮助，如果你不知道某个命令的用法不懂，可以问他，他知道就回告诉你
例如：
man ls 显示ls命令的帮助内容
```

`w`
```
功能：显示登录用户的详细信息
```

`who`
```
功能：显示登录用户
```

`last`
```
功能：查看最近那些用户登录系统
```

`date`
```
功能：系统日期设定
date　显示当前日期时间
date -s 20:30:30　设置系统时间为20:30:30
date -s 2002-3-5　设置系统时期为2003-3-5
date -s "060520 06:00:00"　设置系统时期为2006年5月20日6点整。
```

`clock`
```
功能：时钟设置
clock –r　对系统Bios中读取时间参数
clock –w　将系统时间(如由date设置的时间)写入Bios
```

`uname`
```
功能：查看系统版本
uname -a　显示操作系统内核的version
例如：
uname -a
Linux Sarge 2.6.8-2-386 #1 Tue Aug 16 12:46:35 UTC 2005 i686 GNU/Linux
```

`关闭和重新启动系统命令`
```
reboot　 重新启动计算机
shutdown -r now 重新启动计算机，停止服务后重新启动计算机
shutdown -h now 关闭计算机，停止服务后再关闭系统
halt 关闭计算机
一般用shutdown -r now,在重启系统是，关闭相关服务，shutdown -h now也是如此。
```

`su`
```
功能：切换用户
su - 切换到root用户
su - zhoulj 切换到zhoulj用户，
注意：- ，他很关键，使用-，将使用用户的环境变量
```

`Linux查看登录日志`
```
#Linux 查看登录成功的用户信息
命令: last
##最新的登录记录在最前面，所以可以用一下命令来查看。
last | less
#查看登录失败的用户信息
命令: lastb
#查看登录日志
命令:  tail /var/log/secure
```

`Linux修改时区`
```
#查看当前时区
命令 ：
date -R
#修改设置Linux服务器时区
#方法 A
命令 ：
tzselect

#方法 B
#仅限于RedHat Linux 和 CentOS
命令 ：
timeconfig

#方法 C
#适用于Debian
命令 ：
dpkg-reconfigure tzdata
#复制相应的时区文件，替换系统时区文件；或者创建链接文件
cp /usr/share/zoneinfo/$主时区/$次时区 /etc/localtime
#例如：在设置中国时区使用亚洲/上海（+8）
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```

`查看linux操作系统版本`
```
#Ubuntu
cat /etc/os-release
#Centos
lsb_release -a
#All
cat /proc/version
```

`清除 SSH 登录记录`
```
cat /dev/null > /var/log/wtmp
cat /dev/null > /var/log/btmp
cat /dev/null > /var/log/lastlog
cat /dev/null > /var/log/secure
```

`清除 Bash 历史命令`
```
history -c
history -w
#如果只需清除当前会话用过的命令记录，使用 history -r命令清除
```

`查看CPU信息`
```
查看cpu配置
cat /proc/cpuinfo

查看物理cpu个数
grep 'physical id' /proc/cpuinfo | sort -u

查看核心数量
#sort -u 去重排序
#wc -l 显示行数
grep 'core id' /proc/cpuinfo | sort -u | wc -l

查看线程数
grep 'processor' /proc/cpuinfo | sort -u | wc -l
```

`top`
```
功能：查看系统cpu、内存等使用情况
```

`free`
```
功能：查看内存和swap分区使用情况
```

`uptime`
```
功能：现在的时间 ，系统开机运转到现在经过的时间，连线的使用者数量，最近一分钟，五分钟和十五分钟的系统负载
```

`vmstat`
```
功能：监视虚拟内存使用情况
```

`ps`
```
功能：显示进程信息
ps ux 显示当前用户的进程
ps uxwww 显示当前用户的进程的详细信息
ps aux显示所有用户的进程
ps ef 显示系统所有进程信息
```

`kill`
```
功能：干掉某个进程，进程号可以通过ps命令得到
kill -9 1001　将进程编号为1001的程序干掉
kill all -9 apache　将所有名字为apapche的程序杀死，kill不是万能的，对僵死的程序则无效。
```

`Linux查看关机记录`
```
last -x | grep shutdown #命令查看关机记录即可
```

`gzip`
```
功能：压缩文件，gz格式的
注意：生成的文件会把源文件覆盖
gzip -v 压缩文件，并且显示进度
gzip -d 解压缩
gnuzip -f 解压缩
gunzip FileName.gz #解压法1
gzip -d FileName.gz #解压法2
gzip FileName #压缩，只能压缩文件
例如：
gzip a.sh
-rwxr-xr-x    1 root     root           71 12月 18 21:08 a.sh.gz
gzip -d a.sh.gz
-rwxr-xr-x     1 root     root           48 12月 18 21:08 a.sh
```

`zip & uzip`
```
功能：压缩和解压缩zip命令
zip
unzip

zip -r zip001.zip folder1/ folder2/ floder3/   #压缩多个文件/文件夹到zip001.zip内
zip -u zip001.zip folder4/ readme.txt  #更新压缩包内文件，把文件/文件夹添加到已存在的压缩包内
```

`bzip2`
```
功能：bzip2格式压缩命令，注意：生成的文件会把源文件覆盖
bzip2
bunzip2
```

`tar`
```
功能：归档、压缩等，比较重要，会经常使用
-cvf 压缩文件或目录
-xvf 解压缩文件或目录
-tvf 查看压缩文件内容而不解压
-zcvf 压缩文件或，格式tar.gz
-zxvf 解压缩文件或，格式tar.gz
-zcvf 压缩文件或，格式tgz
-zxvf 解压缩文件或，格式tgz
-xjvf 解压tar.bz2文件
-xf 自动识别类型
```

`df`
```
功能：检查文件系统的磁盘空间占用情况。可以利用该命令来获取硬盘被占用了多少空间，目前还剩下多少空间等信息。
参数 功能
-a 列出全部目录
-Ta 列出全部目录，并且显示文件类型
-B显示块信息
-i 以i节点列出全部目录
-h按照日常习惯显示（如：1K、100M、20G）
-x [filesystype]不显示[filesystype]
```

`du`
```
功能：检测一个目录和（递归地）所有它的子目录中的文件占用的磁盘空间。
参数 功能
-s [dirName]显示目录占用总空间
-sk [dirName] 显示目录占用总空间，以k为单位
-sb [dirName] 显示目录占用总空间，以b为单位
-sm [dirName] 显示目录占用总空间，以m为单位
-sc [dirName] 显示目录占用总空间，加上目录统计
-sh [dirName] 只统计目录大小
du -sh * | sort -n   #显示目录大小并排序
du . -h --max-depth=1 | sort -h -r 显示当前文件夹内所有文件的大小并排序
例如：
du -sh /etc
1.3M /etc
```

`mount`
```
功能：使用mount命令就可在Linux中挂载各种文件系统。
格式：mount -t 设备名 挂载点
（1）、mount /dev/sda1 /mnt/filetest
mount -t vfat /dev/hda /mnt/fatfile
mount -t ntfs /dev/hda /mnt/ntfsfile
mount -t iso9660 /dev/cdrom /mnt/cdrom
mount -o 设备名 挂载点
（2）、使用usb设备
modprobe usb-storage
mkdir /mnt/usb
mount -t auto /dev/sdx1 /mnt/usb
umount /mnt/usb
```

`mkswap`
```
功能：使用mkswap命令可以创建swap空间，如：
mkswap -c /dev/hda4
swapon /dev/hda4 #启用新创建的swap空间，停用可使用swapoff命令
```

`fdisk`
```
功能：对磁盘进行分区
fdisk /dev/xxx 格式化xxx设备(xxx是指磁盘驱动器的名字，例如hdb，sdc)
fdisk -l 显示磁盘的分区表
```

`mkfs`
```
功能：格式化文件系统，可以指定文件系统的类型，如ext2、ext3、fat、ntfs等
格式1：mkfs.ext3 options /dev/xxx
格式2：mkfs -t ext2 options /dev/xxx
参数 功能
-b 块大小
-i 节点大写
-m 预留管理空间大小
例如：
debian:~#mkfs.ext3 /dev/sdb1
```

`e2fsck`
```
功能：磁盘检测
e2fsck /dev/hda1　检查/dev/hda1是否有文件系统错误，提示修复方式
e2fsck -p /dev/hda1　检查/dev/hda1是否有错误，如果有则自动修复
e2fsck -y /dev/hda1　检查错误，所有提问均于yes方式执行
e2fsck -c /dev/hda1　检查磁盘是否有坏区
```

`tune2fs`
```
功能：调整ext2/ext3文件的参数
参数 功能
-l 查看文件系统信息
-c 设置强制自检的挂载次数
-i 设置强制自检的间隔时间，单位天
-m 保留块的百分比
-j 将ext2文件系统转换成ext3格式
tune2fs -l /dev/sda1
```

`dd`
```
功能：功能：把指定的输入文件拷贝到指定的输出文件中，并且在拷贝过程中可以进行格式转换。
跟DOS下的diskcopy命令的作用类似。
dd if=/dev/fd0 of=floppy.img　将软盘的内容复制成一个镜像
dd if=floppy.img of=/dev/fd0　将一个镜像的内容复制到软盘，做驱动盘的时候经常用。
```

`groupadd`
```
功能：添加组
groupadd test1 添加test1组
groupadd -g 1111 test2 添加test2组，组id为1111
```

`useradd`
```
功能：添加用户
useradd user1 添加用户user1，home为/home/user1，组为user1
useradd -g test1 -m -d /home/test1 -s /bin/bash test1 添加用户test1，home为/home/test1，组为test1
user list　显示已登陆的用户列表
```

`passwd`
```
功能：更改用户密码
passwd user1　修改用户user1的密码
passwd -d root　将root用户的密码删除
```

`userdel`
```
功能：删除用户
userdel user1　删除user1用户
```

`chown`
```
功能：改变文件或目录的所有者
chown user1 /dir　将/dir目录设置为user1所有
chown -R user1.user1 /dir　将/dir目录下所有文件和目录，设置为user1所有,组为user1。-R递归到下面的每个文件和目录
```

`chgrp`
```
功能：改变文件或目录的所有组
chgrp user1 /dir　将/dir目录设置为user1所有
```

`chmod`
```
功能：改变用户的权限
chmod a+x file　将file文件设置为可执行，脚本类文件一定要这样设置一个，否则得用bash file才能执行
chmod 666 file　将文件file设置为可读写
chmod 750 file 将文件file设置为，所有者为完全权限，同组可以读和执行，其他无权限
```

`id`
```
功能：显示用户的信息，包括uid、gid等
id zhoulj
uid=500(zhoulj) gid=500(zhoulj) groups=500(zhoulj)
```

### 3. Linux基本网络相关命令
`ssh`
```bash
功能：远程登陆到其他UNIX主机ssh -l user1 192.168.1.2 使用用户名user1登陆到192.168.1.2
使用用户名user1登陆到192.168.1.2ssh root@127.0.0.1 -p 65200
访问指定ssh端口
```

`Linux BBR`
```bash
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

`启动网络的命令`
```bash
/etc/init.d/network stop #停止网络
/etc/init.d/network start #启动网络
```

`使用mtr进行网络排错`
教程地址:[https://www.ucloud.cn/yun/121703.html](https://www.ucloud.cn/yun/121703.html)

```bash
# Debian & Ubuntu
apt -y install mtr-tiny

# RHEL & CentOS
yum -y install mtr

#如果软件仓库没有，可以下载 MTR 源码编译安装。详细请查看官方说明。https://github.com/traviscross/mtr如果测试连接去程应在本地电脑上运行 MTR 测试服务器 IP。如果测试回程则在服务器上操作，测试本地网络公网 IP。
#对于大多数服务，如网站或代理用途，应更关注回程质量。因为主要是服务器返回数据到本地，而本地发送到服务器的数据量很少。下面只说 Linux MTR 用法，Windows 图形操作界面很容易就不做介绍了，
#例如测试到 8.8.8.8 的连接情况。命令中的 r 参数表示向目标主机发送 10 个 ICMP 数据包，然后输出报告。如果不带参数，则会不间断运行，取消按 q 键退出。
mtr -r 8.8.8.8
```

`ifconfig`
```bash
功能：显示修改网卡的信息ifconfig 显示网络信息
ifconfig eth0 显示eth0网络信息
修改网络信息：ifconfig eth0 192.168.1.1 netmask 255.255.255.0 设置网卡1的地址192.168.1.1，掩码为255.255.255.0
ifconfig eth0:1 192.168.1.2　 捆绑网卡1的第二个地址为192.168.1.2
ifconfig eth0:x 192.168.1.n　 捆绑网卡1的第n个地址为192.168.1.n
例如：ifconfig eth0:1 192.168.1.11
```

`route`
```bash
功能：显示当前路由设置情况route 显示当前路由设置情况，比较慢一般不用。
route add -net 10.0.0.0 netmask 255.255.0.0 gw 192.168.1.254 添加静态路由
route del -net 10.0.0.0 netmask 255.255.0.0 gw 192.168.1.254 添加静态路由
route add default gw 192.168.1.1 metric1　 设置192.168.1.1为默认的路由
route del default　 将默认的路由删除
```

`netstat`
```bash
功能：显示网络状态netstat -an 查看网络端口信息netstat -nr 查看路由表信息，比route快多了
```

`网络排错`
```bash
pingtraceroute功能：路由跟踪traceroutetraceroute 207.68.173.7
nslookup命令功能：域名解析排错例如：nslookup baidu.com
```

`Linux Prohibit mail ports`
/BT/PT/SPAM

```bash
wget -N --no-check-certificate <https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/ban_iptables.sh> && chmod +x ban_iptables.sh && bash ban_iptables.sh
```

`Linux 检测流媒体解锁`

```bash
bash <(curl -L -s check.unlock.media)

bash <(curl -L -s check.unlock.media) -M 4 # 只检测ipv4结果

bash <(curl -L -s check.unlock.media) -M 6 # 只检测ipv6结果
```

`Linux wget`
```bash
#wget下载网站文件到指定目录wget -P /home/wwwroot  www.xxxx.com/sa.apk
#wget下载一个目录下的所有文件wget -c -r -np -k -L -R index.html -P /home/wwwroot www.nasa.com/sa/
wget -c -r -np -k -L -p  <http://docs.openstack.org/liberty/install-guide-rdo/>#参数说明-c 断点续传
-r 递归下载，下载指定网页某一目录下（包括子目录）的所有文件
-nd 递归下载时不创建一层一层的目录，把所有的文件下载到当前目录
-np 递归下载时不搜索上层目录，如wget -c -r www.xianren.org/pub/path/
没有加参数-np，就会同时下载path的上一级目录pub下的其它文件-nH : 不要将文件保存到主机名文件夹-k 将绝对链接转为相对链接，下载整个站点后脱机浏览网页，最好加上这个参数
-R index.html : 不下载 index.html 文件
-L 递归时不进入其它主机，如wget -c -r www.xianren.org/
-p 下载网页所需的所有文件，如图片等
-A 指定要下载的文件样式列表，多个样式用逗号分隔
$ wget -r -A.pdf <http://url-to-webpage-with-pdfs/>-i 后面跟一个文件，文件内指明要下载的URL
#wget -O 下载并使用不同的文件名存储wget -O taglist.zip <http://www.vim.org/scripts/download_script.php?src_id=7701>
```

`Linux查看端口占用并如何关闭占用端口的进程`
```bash
#查看所有端口netstat -tunlp#查看特定端口netstat -tunlp ｜ grep 60010
#杀死进程，2970的进程PIDkill -9 2970
```

`telnet`
```bash
功能：登陆到远程主机例如：telnet 192.168.1.5
```

`Mac install shadowrocket`
```bash
#要求M1芯片如果您无法在Mac M1 上下载或更新Shadowrocket，可以清空Mac App Store 缓存后尝试更新下载。
打开“终端”App，复制如下两条脚本到命令行然后按回车执行
rm -rf /Users/$(whoami)/Library/Caches/com.apple.appstore
rm -rf /Users/$(whoami)/Library/Caches/com.apple.appstoreagent
```

`VPS测试三网回程路由`
```bash
wget -qO- git.io/besttrace | bash
```

`Hyper_V调整Centos/RedHat显示分辨率`

```bash
su root
grubby --update-kernel=ALL --args="video=hyperv_fb:1920x1080"reboot
```

`Linux设置Warp添加ipv4/ipv6`
```
#添加 WARP Wire­Guard 双栈全局网络
bash <(curl -fsSL git.io/warp.sh) menu

#检测流媒体
bash <(curl -L -s check.unlock.media)

# 只检测ipv4结果
bash <(curl -L -s check.unlock.media) -M 4 

# 只检测ipv6结果
bash <(curl -L -s check.unlock.media) -M 6 
```