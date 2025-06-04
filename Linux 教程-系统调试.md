
## Linux系统调试常用命令
在 Linux 中，获取系统信息和监控系统资源的操作是非常常见的任务。以下是一些常用的命令和工具，以及一些相关的系统文件，用于获取 Linux 系统信息和监控系统资源。

### **1\. 基本系统信息**

### `uname` 命令

`uname` 命令用于显示系统信息。

*   **查看内核版本：** 
`uname -r`

*   **查看操作系统：** 
`uname -a`

```
$ uname -r
5.4.0-91-generic

$ uname -a
Linux example-host 5.4.0-91-generic#102-Ubuntu SMP Fri Nov 5 16:31:28 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
```

### **2\. CPU 信息**

### `lscpu` 命令

`lscpu` 命令用于显示 CPU 信息。

```
$ lscpu
Architecture:                    x86_64
CPU op-mode(s):                  32-bit, 64-bit
Byte Order:                      Little Endian
CPU(s):                          4
On-line CPU(s) list:             0-3
Thread(s) per core:              2
Core(s) per socket:              2
Socket(s):                       1
NUMA node(s):                    1
Vendor ID:                       GenuineIntel
CPU family:                      6
Model:                           142
Model name:                      Intel(R) Core(TM) i5-8265U CPU @ 1.60GHz
Stepping:                        12
```

### **3\. 内存信息**

### `free` 命令

`free` 命令用于显示内存使用情况。

```
$ free -h
              total        used        free      shared  buff/cache   available
Mem:           7.7G        2.5G        1.2G        340M        3.9G        4.8G
Swap:          2.0G          0B        2.0G
```

### **4\. 硬盘信息**

### `df` 命令

`df` 命令用于显示磁盘空间使用情况。

```
$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        19G   11G  7.1G  61% /
```

### **5\. 进程和系统负载**

### `ps` 命令

`ps` 命令用于显示进程信息。

*   **显示所有进程：** `ps aux`
*   **按用户显示进程：** `ps -U username`
*   **按进程树显示：** `ps -e --forest`

```
$ ps aux
USER       PID  %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
user1     1234  10.0  1.0 123456  5678 pts/1    S+   Jan01  10:00 ./example

$ ps -e --forest
  PID TTY      STAT   TIME COMMAND
 1234 pts/1    S+     0:00  \_ ./example
```

### `top` 命令

`top` 命令用于动态显示系统资源使用情况。

```
$ top
```

### **6\. 网络信息**

### `ifconfig` 命令

`ifconfig` 命令用于显示网络接口信息。

```
$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.2  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::1234:5678:abcd:efgh  prefixlen 64  scopeid 0x20<link>
        ether 12:34:56:78:ab:cd  txqueuelen 1000  (Ethernet)
        RX packets 123456  bytes 78901234 (78.9 MB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 56789  bytes 12345678 (12.3 MB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

### `netstat` 命令

`netstat`

命令用于显示网络连接、路由、接口等信息。

*   **显示所有网络连接：** `netstat -a`
*   **显示路由表：** `netstat -r`
*   **显示网络接口信息：** `netstat -i`

```
$ netstat -a
$ netstat -r
$ netstat -i
```

### **7\. 日志文件**

Linux 系统的日志文件通常存储在 `/var/log`目录下，其中一些常见的日志文件包括：

*   **系统日志：** `/var/log/syslog` 或 `/var/log/messages`
*   **安全日志：** `/var/log/auth.log` 或 `/var/log/secure`
*   **应用程序日志：** `/var/log/application.log`

```
$ cat /var/log/syslog
$ cat /var/log/auth.log
$ cat /var/log/application.log
```

### **8.** `dmesg` 命令

`dmesg` 命令用于显示内核环缓冲区的信息，提供有关系统启动和硬件事件的详细信息。

```
$ dmesg
```

### **9.** `lsblk` 命令

`lsblk` 命令用于列出块设备（如磁盘分区）的信息。

```
$ lsblk
```

### **10.** `uptime` 命令

`uptime` 命令用于显示系统的运行时间和平均负载。

```
$ uptime
```

### **11.** `ip` 命令

`ip` 命令用于配置和显示网络设备、路由、策略路由等网络子系统的状态。

```
$ ip addr
$ ip route
```

### **12.** `/proc` 文件系统

Linux 提供了 `/proc` 文件系统，通过读取其中的文件，可以获取有关系统和进程的信息。

*   **CPU 信息：** `/proc/cpuinfo`
*   **内存信息：** `/proc/meminfo`
*   **进程信息：** `/proc/[PID]/status`

```
$ cat /proc/cpuinfo
$ cat /proc/meminfo
$ cat /proc/1234/status
```

### **13.** `lsof` 命令

`lsof` 命令用于列出打开文件的信息，可以用于查看进程使用的文件和网络连接。

```
$ lsof
```

### **14.** `journalctl` 命令

`journalctl` 命令用于查询和显示 `systemd`日志消息。

```
$ journalctl
```

### **15.** `sar` 命令

`sar` 命令用于收集、报告和保存系统活动信息，例如 CPU 使用率、内存使用率、磁盘活动等。

```
$ sar
```

### **16.** `vmstat` 命令

`vmstat` 命令用于报告虚拟内存统计信息，包括进程、内存、磁盘、交换等。

```
$ vmstat 1
```

### **17.** `iostat` 命令

`iostat` 命令用于报告 CPU 使用率和磁盘 I/O 统计信息。

```
$ iostat
```

### **18.** `nstat` 命令

`nstat` 命令用于显示网络统计信息。

```
$ nstat
```

### **19.** `iftop` 命令

`iftop` 命令用于实时显示网络带宽使用情况。

```
$ iftop
```

### **20.** `htop` 命令

`htop` 命令是一个交互式的系统监视工具，用于查看进程、内存、CPU 使用情况。

```
$ htop
```

### **21.** `lsmod` 命令

`lsmod` 命令用于列出当前加载的内核模块。

```
$ lsmod
```

### **22.** `ulimit` 命令

`ulimit` 命令用于查看和设置用户级别资源限制。

```
$ ulimit -a
```

### **23.** `watch` 命令

`watch` 命令用于周期性地执行指定的命令，并显示结果。

```
$ watch -n 1 'ps aux'
```

### **24.** `tcpdump` 命令

`tcpdump` 命令用于捕获和分析网络数据包。

```
$ tcpdump -i eth0
```

### **25.** `strace` 命令

`strace` 命令用于跟踪进程执行时的系统调用和信号。

```
$ strace -p PID
```

以上是一些系统监控和调试工具，用于深入了解系统运行状况、性能特征和调试问题。这些工具在系统管理、性能优化和故障排查中都有广泛的应用。以上命令还有很多其他高级用法，在平时调试中我们可根据具体情况查看每个命令的详细参数和使用方法。