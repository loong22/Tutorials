### 1. 生成文件大小和实际占空间大小一样的文件

```sh

dd if=/dev/zero of=50M.file bs=1M count=50

dd if=/dev/zero of=20G.file bs=1G count=20

bs=1M表示每一次读写1M数据，count=50表示读写
50次，这样就指定了生成文件的大小为50M。bs参数还可以进一步细分为ibs和obs两种，为读操作与写操作分别指定不同的Buffer大小。
```
### 2. 生成文件大小固定，但实际不占空间命令
```sh
dd if=/dev/zero of=1G.img bs=1M seek=1000 count=0

这里用了一个新的命令seek，表示略过1000个Block不写（这里Block按照bs的定义是1M），count=0表示写入0个Block。用ls(查看文件大小)命令看新生成的文件，大小可以看出是1000M。但是再用du（查看文件占用空间）一看，实际占用硬盘大小只有0M。

```
### 3. 命令详解
```sh
dd命令用法：

dd [选项]

if =输入文件（或设备名称）。

of =输出文件（或设备名称）。

ibs = bytes 一次读取bytes字节，即读入缓冲区的字节数。

skip = blocks 跳过读入缓冲区开头的ibs*blocks块。

obs = bytes 一次写入bytes字节，即写入缓冲区的字节数。

bs = bytes 同时设置读/写缓冲区的字节数（等于设置ibs和obs）。

cbs = byte 一次转换bytes字节。

count=blocks 只拷贝输入的blocks块。

conv = ASCII 把EBCDIC码转换为ASCIl码。

conv = ebcdic 把ASCIl码转换为EBCDIC码。

conv = ibm 把ASCIl码转换为alternate EBCDIC码。

conv = block 把变动位转换成固定字符。

conv = ublock 把固定位转换成变动位。

conv = ucase 把字母由小写转换为大写。

conv = lcase 把字母由大写转换为小写。

conv = notrunc 不截短输出文件。

conv = swab 交换每一对输入字节。

conv = noerror 出错时不停止处理。

conv = sync 把每个输入记录的大小都调到ibs的大小（用NUL填充）。

iconv命令用法：

iconv [选项…] [文件…]

输入/输出格式规范：

-f, –from-code=原始文本编码

-t, –to-code=输出文本编码

信息：

-l, –list 列举所有已知的字符集

输出控制：

-c 从输出中忽略无效的字符

-o, –output=FILE 输出文件

-s, –silent 关闭警告

–verbose 打印进度信息

-?, –help 给出该系统求助列表

–usage 给出简要的用法信息

-V, –version 打印程序版本号
```