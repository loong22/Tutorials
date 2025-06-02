# 转载地址
https://blog.inkuang.com/2019/517/

简介

Linux 系统中的 Swap 分区，即交换分区，类似于 Windows 的虚拟内存，其作用可简单的描述为：当系统的物理内存不够用的时候，将暂时不用的数据存放到交换空间所在的硬盘上，从而可以腾出内存来让别的程序运行。在这里不讨论直接划出一部分硬盘作为交换分区的情况，只介绍通过添加 Swap 文件来设置交换分区的方式。添加 Swap 文件

随便进入一个目录用于后续存放 Swap 文件，也可以直接放在根目录，这里放在 /var 目录下。

```cd /var```

使用 dd 命令生成一个文件块，大小为自己想设置的 Swap 分区大小，这里生成一个名为 swapfile 的文件，大小设为 1G。```dd if=/dev/zero of=swapfile bs=1M count=1024```

将该文件设为 Swap 文件（格式化）。

```mkswap swapfile```

激活 Swap 文件（启用虚拟内存）。

```swapon swapfile```

检查 Swap 是否正确。

```swapon -s```

另外为了安全建议将交换分区文件权限设为 0600 或 0644 ，执行以下命令。

```chmod 0600 swapfile```

此时已经成功添加了交换分区，可以使用 free 之类的命令查看验证。



但这样重启后 Swap 分区会丢失，需要重新激活才行，所以我们一般在 fstab 文件为其添加开机自动挂载设置。

```vi /etc/fstab```

在最后增加以下内容：

```/var/swapfile swap swap defaults 0 0```

也可以直接执行如下命令来添加：

```echo \"/var/swapfile swap swap defaults 0 0\" >> /etc/fstab```

删除 Swap 文件\n这里以我们上面设置的 Swap 文件（文件名、文件路径）作为示范。

首先将 Swap 文件取消激活：

```swapoff /var/swapfile```

然后删除我们设置的 Swap 文件：

```rm /var/swapfile```

最后再编辑 /fstab 文件删除掉自动挂载 Swap 的设置即可：

```vi /etc/fstab```