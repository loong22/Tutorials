
Linux中普通用户用sudo执行命令时报"xxx is not in the sudoers file.This incident will be reported"错误
解决方法就是在/etc/sudoers文件里给该用户添加权限。

### 1. 切换到root用户下
方法为直接在命令行输入：
`su`
然后输入密码（即你的登录密码，且密码默认不可见）

### 2. /etc/sudoers文件默认是只读的，对root来说也是，因此需先添加sudoers文件的写权限,命令是:即执行操作：

`chmod u+w /etc/sudoers`

### 3. 编辑sudoers文件 即执行：`vi /etc/sudoers` 找到这行 root
ALL=(ALL) ALL,在他下面添加xxx ALL=(ALL) ALL (这里的xxx是你的用户名)
ps:这里说下你可以sudoers添加下面四行中任意一条
```
youuser ALL=(ALL) ALL
%youuser ALL=(ALL) ALL
youuser ALL=(ALL) NOPASSWD: ALL
%youuser ALL=(ALL) NOPASSWD: ALL
```
第一行:允许用户youuser执行sudo命令(需要输入密码).
第二行:允许用户组youuser里面的用户执行sudo命令(需要输入密码).
第三行:允许用户youuser执行sudo命令,并且在执行的时候不输入密码.
第四行:允许用户组youuser里面的用户执行sudo命令,并且在执行的时候不输入密码.

### 4. 撤销sudoers文件写权限,命令: 
`chmod u-w /etc/sudoers`
