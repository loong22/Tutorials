# Ubuntu
## 1. 安装基础依赖

```
sudo apt update 

sudo apt install -y gcc nano vim curl wget g++ libcairo2-dev libjpeg-turbo8-dev libpng-dev libtool-bin libossp-uuid-dev

sudo apt install -y libavcodec-dev libavformat-dev libavutil-dev libswscale-dev build-essential libpango1.0-dev libssh2-1-dev libvncserver-dev libtelnet-dev libpulse-dev libvorbis-dev libwebp-dev

# 安装FreeRDP
sudo apt install -y freerdp2-dev freerdp2-x11 


sudo apt install -y libwebsockets-dev
```

## 2. 安装Apache Tomcat
```
#安装java环境
sudo apt install -y default-jdk

#确保安装java环境
java --version

#安装Apache Tomcat
sudo apt install -y tomcat9 tomcat9-admin tomcat9-common tomcat9-user

#开机自启
systemctl enable --now tomcat9

#查看运行状态
systemctl status tomcat9

#打开8080端口
ufw allow 8080/tcp

```

## 3. 构建Guacamole Server
```
#安装
wget https://iplcn2.com/file/guacamole-server-1.5.5.tar.gz

tar xzf ~/guacamole-server-1.5.5.tar.gz

cd ~/guacamole-server-1.5.5/

./configure --disable-guacenc --with-init-dir=/etc/init.d

make 

sudo make install

sudo ldconfig

#设置配置文件
sudo mkdir -p /etc/guacamole/{extensions,lib}

sudo vim /etc/guacamole/guacd.conf

#内容如下:
#---------------------

[daemon] 
pid_file = /var/run/guacd.pid 
#log_level = debug

[server] 
#bind_host = localhost 
bind_host = 127.0.0.1 
bind_port = 4822 

#[ssl] 
#server_certificate = /etc/ssl/certs/guacd.crt 
#server_key = /etc/ssl/private/guacd.key

#---------------------

#重启服务
sudo systemctl daemon-reload

sudo systemctl restart guacd

sudo systemctl enable guacd

#查看状态
sudo systemctl status guacd

```

## 4. 安装Web App
```

wget https://iplcn2.com/file/guacamole-1.5.5.war

sudo cp guacamole-1.5.5.war /var/lib/tomcat9/webapps/guacamole.war

```

## 5.配置Guacamole Server服务
```

#创建环境变量
echo "GUACAMOLE_HOME=/etc/guacamole" | tee -a /etc/default/tomcat

echo "export GUACAMOLE_HOME=/etc/guacamole" | tee -a /etc/profile

#创建配置文件
sudo vim /etc/guacamole/guacamole.properties

#内容如下
#---------
guacd-hostname: localhost 
guacd-port: 4822
#---------

```

## 6.设置Guacamole Database Authentication

先安装MySql服务

https://green.cloud/docs/how-to-allow-remote-connections-to-mysql-on-ubuntu/

```
cd ~/ 

wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-j-8.3.0.tar.gz

tar -xf mysql-connector-j-8.3.0.tar.gz

sudo cp mysql-connector-j-8.3.0/mysql-connector-j-8.3.0.jar /etc/guacamole/lib/

wget https://downloads.apache.org/guacamole/1.5.5/binary/guacamole-auth-jdbc-1.5.5.tar.gz

tar -xf guacamole-auth-jdbc-1.5.5.tar.gz

sudo mv guacamole-auth-jdbc-1.5.5/mysql/guacamole-auth-jdbc-mysql-1.5.5.jar /etc/guacamole/extensions/


##### **Installing MySQL**

sudo apt install -y mysql-server

sudo systemctl start mysql.service

sudo mysql_secure_installation


#Now login to the database server using the root user and password

sudo mysql -u root -p

#Once connected, create a database and user for Guacamole

CREATE DATABASE guacamole_db;

CREATE USER 'guacamole_user'@'localhost' IDENTIFIED BY 'Ubuntu123456';

GRANT SELECT,INSERT,UPDATE,DELETE ON guacamole_db.* TO 'guacamole_user'@'localhost';

FLUSH PRIVILEGES;

QUIT

#Switch to the extracted JDBC plugin path:

cd guacamole-auth-jdbc-1.5.5/mysql/schema

cat *.sql | sudo mysql -u root -p guacamole_db

#Provide the password for the **root** user to import the schemas. Next, modify the properties of Guacamole


#编辑配置文件
sudo vim /etc/guacamole/guacamole.properties

###MySQL properties 
mysql-hostname: 127.0.0.1 
mysql-port: 3306 
mysql-database: guacamole_db 
mysql-username: guacamole_user 
mysql-password: Ubuntu123456


sudo systemctl restart tomcat9 guacd

```


## 7. 通过Web进行访问

 http://ip-or-domain-name:8080/guacamole

用户名密码均为  guacadmin

```
#Ubuntu 安装VNC
sudo apt install -y tigervnc-standalone-server
```

## 方法1
基于Ubuntu 22.04
https://green.cloud/docs/how-to-install-guacamole-remote-desktop-tool-on-ubuntu-22-04/

## 方法2 
[Guacamole: 在浏览器中访问远程桌面环境 - 静心前行](https://pickstar.today/2023/03/guacamole-%E5%9C%A8%E6%B5%8F%E8%A7%88%E5%99%A8%E4%B8%AD%E8%AE%BF%E9%97%AE%E8%BF%9C%E7%A8%8B%E6%A1%8C%E9%9D%A2%E7%8E%AF%E5%A2%83/)

[Setup of a Guacamole HTML5 remote XFCE desktop via VNC for Ubuntu 22.04 | axaneco’s tech blog](https://blog.axaneco.net/doc/2023/04/03/remote-vm-with-xfce.html)

[如何在Ubuntu 20.04 上安装 Xrdp 服务器（远程桌面）-阿里云开发者社区](https://developer.aliyun.com/article/762186)


# CentOS 7 安装Guacamole及VncServer

## [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85guacamole "下载安装guacamole")下载安装guacamole

### [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#%E9%85%8D%E7%BD%AE%E7%8E%AF%E5%A2%83 "配置环境")配置环境

安装必要环境

Code

|   |   |
|---|---|
|1  <br>2  <br>3|rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro  <br>rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm  <br>yum update -y|

安装依赖包

Code

|                                      |                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1  <br>2  <br>3  <br>4  <br>5  <br>6 | yum -y install cairo-devel libjpeg-devel libpng-devel uuid-devel    <br>yum -y install ffmpeg-devel  freerdp-devel pango-devel libssh2-devel   <br>yum -y install libtelnet-devel libvncserver-devel pulseaudio-libs-devel    <br>yum -y install openssl-devel libvorbis-devel libwebp-devel  <br>yum -y install freerdp-plugins  <br>yum -y install gcc |

### [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#%E4%B8%8B%E8%BD%BD%E5%AE%89%E8%A3%85%E5%8C%85 "下载安装包")下载安装包

下载地址

[https://guacamole.apache.org/releases/1.2.0/](https://guacamole.apache.org/releases/1.2.0/)

下载server和client

### [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#guacamole-server%E5%AE%89%E8%A3%85 "guacamole-server安装")guacamole-server安装

Code

|   |   |
|---|---|
|1  <br>2  <br>3|tar zxf guacamole-server-1.2.0.tar.gz -C /opt  <br>cd /opt/guacamole-server-1.2.0  <br>./configure --with-init-dir=/etc/init.d|

确认所有协议是否都支持

shell

|   |   |
|---|---|
|1  <br>2  <br>3  <br>4  <br>5  <br>6  <br>7  <br>8  <br>9  <br>10  <br>11  <br>12  <br>13  <br>14  <br>15  <br>16  <br>17  <br>18  <br>19  <br>20  <br>21  <br>22  <br>23  <br>24  <br>25  <br>26  <br>27  <br>28  <br>29  <br>30  <br>31  <br>32  <br>33  <br>34  <br>35  <br>36  <br>37  <br>38  <br>39  <br>40|guacamole-server version 1.2.0  <br>------------------------------------------------  <br>  <br>   Library status:  <br>  <br>     freerdp2 ............ yes  <br>     pango ............... yes  <br>     libavcodec .......... yes  <br>     libavformat.......... yes  <br>     libavutil ........... yes  <br>     libssh2 ............. yes  <br>     libssl .............. yes  <br>     libswscale .......... yes  <br>     libtelnet ........... yes  <br>     libVNCServer ........ yes  <br>     libvorbis ........... yes  <br>     libpulse ............ yes  <br>     libwebsockets ....... no  <br>     libwebp ............. yes  <br>     wsock32 ............. no  <br>  <br>   Protocol support:  <br>  <br>      Kubernetes .... no  <br>      RDP ........... yes  <br>      SSH ........... yes  <br>      Telnet ........ yes  <br>      VNC ........... yes  <br>  <br>   Services / tools:  <br>  <br>      guacd ...... yes  <br>      guacenc .... yes  <br>      guaclog .... yes  <br>  <br>   FreeRDP plugins: /usr/lib64/freerdp2  <br>   Init scripts: /etc/init.d  <br>   Systemd units: no  <br>  <br>Type "make" to compile guacamole-server.|

确认完成后使用make安装

Code

|   |   |
|---|---|
|1|make && make install|

启用guacd服务

shell

|   |   |
|---|---|
|1  <br>2  <br>3|# /etc/init.d/guacd start  <br>Starting guacd: guacd[2240]: INFO:      Guacamole proxy daemon (guacd) version 1.2.0 started  <br>SUCCESS|

### [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#%E5%AE%89%E8%A3%85guacamole-client "安装guacamole client")安装guacamole client

#### [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#%E9%A6%96%E5%85%88%E5%AE%89%E8%A3%85jdk%E5%92%8Ctomcat "首先安装jdk和tomcat")首先安装jdk和tomcat

这里使用openjdk11

Code

|   |   |
|---|---|
|1  <br>2|yum update  <br>yum install java-11-openjdk-devel|

安装完成后查看java版本

Code

|   |   |
|---|---|
|1|java -version|

可以通过这个命令设置默认的java版本

Code

|   |   |
|---|---|
|1|alternatives --config java|

设置 JAVA_HOME 环境变量

首先，定位java安装路径

Code

|   |   |
|---|---|
|1|update-alternatives --config java|

确定java路径后，复制需要的java版本，编辑 `.bash_profile`文件

Code

|   |   |
|---|---|
|1|vim .bash_profile|

在文件最下方，添加JAVA_HOME路径

Code

|   |   |
|---|---|
|1|JAVA_HOME=”/your/installation/path/”|

安装tomcat

Code

|   |   |
|---|---|
|1|yum install tomcat|

将war包部署到webapp中

shell

|   |   |
|---|---|
|1  <br>2  <br>3  <br>4  <br>5  <br>6  <br>7  <br>8  <br>9  <br>10|# find / -name webapps  <br>/usr/share/tomcat/webapps  <br>find: ‘/proc/10235’: No such file or directory  <br>find: ‘/run/user/1000/gvfs’: Permission denied  <br>/var/lib/tomcat/webapps  <br># cd /usr/share/tomcat/webapps/  <br># cp /root/guacamole/guacamole-1.2.0.war guacamole.war  <br># ll  <br>total 11964  <br>-rw-r--r--. 1 root root 12249847 Jul  3 11:02 guacamole.war|

建立配置文件

Code

|   |   |
|---|---|
|1  <br>2  <br>3  <br>4  <br>5|cd /usr/share/tomcat  <br>mkdir .guacamole  <br>cd .guacamole  <br>vim  user-mapping.xml  <br>vim  guacamole.properties|

在`guacamole.properties`中配置如下

Code

|   |   |
|---|---|
|1  <br>2|guacd-hostname: localhost  <br>guacd-port:     4822|

在`user-mapping.xml`中配置如下

Code

|                                                                                                                                                                                                                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1  <br>2  <br>3  <br>4  <br>5  <br>6  <br>7  <br>8  <br>9  <br>10  <br>11  <br>12  <br>13  <br>14  <br>15  <br>16  <br>17  <br>18  <br>19  <br>20  <br>21  <br>22  <br>23  <br>24  <br>25  <br>26  <br>27  <br>28 | <user-mapping>  <br>    <authorize  <br>            username="admin"  <br>            password="登录账户密码">  <br>  <br>        <!-- First authorized connection -->  <br>        <connection name="ssh-ame">  <br>            <protocol>ssh</protocol>  <br>            <param name="hostname">ip地址</param>  <br>            <param name="port">22</param>  <br>        </connection>  <br>  <br>        <!-- Second authorized connection -->  <br>        <connection name="vnc-graph">  <br>            <protocol>vnc</protocol>  <br>            <param name="hostname">localhost</param>  <br>            <param name="port">5901</param>  <br>            <param name="password">vnc密码</param>  <br>        </connection>  <br>  <br>        <!-- Third authorized connection -->  <br>        <connection name="ssh-graph">  <br>            <protocol>ssh</protocol>  <br>            <param name="hostname">localhost</param>  <br>            <param name="port">22</param>  <br>        </connection>  <br>    </authorize>  <br></user-mapping> |

启用tomcat服务

Code

|   |   |
|---|---|
|1|systemctl start tomcat|

启动成功后，访问[http://localhost:8080/guacamole即可访问](http://localhost:8080/guacamole%E5%8D%B3%E5%8F%AF%E8%AE%BF%E9%97%AE)

开启防火墙8080端口

Code

|   |   |
|---|---|
|1  <br>2|firewall-cmd --zone=public --add-port=8080/tcp --permanent  <br>firewall-cmd --reload|

## [](https://lemoncat.xyz/2020/07/11/CentOS%207%20%E5%AE%89%E8%A3%85Guacamole/#%E9%85%8D%E7%BD%AE%E5%AE%89%E8%A3%85vncserver "配置安装vncserver")配置安装vncserver

首先安装vncserver


# VNC持久运行命令

在用户路径下的.vnc文件夹内修改xstartup文件和创建vncrun.sh脚本

运行vncrun.sh脚本即可
```
#执行命令，后台运行
./runvnc.sh > /dev/null &
```
## 修改xstartup文件如下
```
#!/bin/sh

unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

#/etc/X11/xinit/xinitrc
# Assume either Gnome or KDE will be started by default when installed
# We want to kill the session automatically in this case when user logs out. In case you modify
# /etc/X11/xinit/Xclients or ~/.Xclients yourself to achieve a different result, then you should
# be responsible to modify below code to avoid that your session will be automatically killed

/usr/bin/xterm &

app_pid=$!
echo $app_pid > $DISPLAY.pid


# 当应用程序终止时，结束 VNC 会话
echo "Application has terminated. Exiting VNC session..."
vncserver -kill :$DISPLAY
```

## 创建vncrun.sh脚本
```
#!/bin/bash

# 无限循环
while true; do
    # 定义 PID 文件范围
    for ((i=1; i<=2; i++)); do
        pid_file=":${i}.pid"
        
        # 检查 PID 文件是否存在
        if [[ -f "$pid_file" ]]; then
            # 读取 PID 文件中的 PID
            pid=$(cat "$pid_file")
            # 检查进程是否还在运行
            if ! ps -p $pid > /dev/null; then
                #echo "Process with PID $pid from file $pid_file is not running."
                # 停止 VNC 会话
                #echo "Killing VNC server :$i"
                vncserver -kill :$i
                # 启动新的 VNC 会话
                #echo "Starting VNC server :$i"
                vncserver :$i
            #else
                #echo "Process with PID $pid from file $pid_file is still running."
            fi
        #else
            #echo "PID file $pid_file does not exist."
        fi
    done

    # 暂停 5 秒
    sleep 5
done
```

## WSL2 Ubuntu启动VNC报错
https://github.com/microsoft/WSL/issues/9303
```
_XSERVTransMakeAllCOTSServerListeners: failed to create listener for unix
```
解决方法
```
sudo /usr/bin/mount -o remount,rw /tmp/.X11-unix
```

### WSL2端口转发到宿主机
```
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=8080 connectaddress=172.31.56.210

netsh interface portproxy show all
```

# Ubuntu安装noVNC
https://www.server-world.info/en/note?os=Ubuntu_22.04&p=desktop&f=8

https://juejin.cn/post/7220383202484289596

https://www.linuxidc.com/Linux/2019-08/159849.htm

https://www.cyberciti.biz/faq/install-and-configure-tigervnc-server-on-ubuntu-18-04/

https://aws.amazon.com/cn/blogs/china/how-to-build-access-to-the-ubuntu-22-desktop-through-a-browser-using-novnc/

# Ubuntu安装桌面

https://linux.cn/article-13408-1.html