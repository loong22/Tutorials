
### 1. HOST 本地DNS解析
`vi /etc/hosts`
添加规则 例如:
`223.231.234.33 www.baidu.com`
### 2. 网卡配置文件DNS服务地址
`vi /etc/sysconfig/network-scripts/ifcfg-eth0`
添加规则 例如:
DSN1=’114.114.114.114’

2.1 debian系统配置文件位置为：/etc/network/interfaces
修改后保存配置后，运行/etc/init.d/networking restart
网络配置就改变了
2.2 redhat系统配置文件位置为：/etc/sysconfig/network-scripts/ifcfg-eth0

```
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=eth0
UUID=e5f4078b-ebf0-46bb-9545-1ab724d541bc
DEVICE=eth0
ONBOOT=no
DNS1=8.8.8.8
DNS2=8.8.4.4
```

修改后保存配置后，运行/etc/init.d/network restart
或者service network restart
网络配置就改变了默认DNS的文件的位置为：/etc/resolv.confcat /etc/resolv.conf
search test.com.cn
nameserver 192.168.1.11

### 3. 系统默认DNS配置vi /etc/resolv.conf
添加规则 例如:
`nameserver 114.114.114.114`
#系统解析的优先级1>2>3
