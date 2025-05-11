
### 1. 在服务器上生成新的密钥对

#### 1.1 生成密钥

控制台输入
```sh
$ ssh-keygen -t rsa -b 4096 #<== 建立密钥对
```

输出内容
```javascript
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): <== 按 Enter
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): <== 输入密钥锁码，或直接按 Enter 留空
Enter same passphrase again: <== 再输入一遍密钥锁码
Your identification has been saved in /root/.ssh/id_rsa. <== 私钥
Your public key has been saved in /root/.ssh/id_rsa.pub. <== 公钥
The key fingerprint is:
****************************
```

此时生成的两个密钥文件`id_rsa`和`id_rsa.pub`在 root 目录下的`.ssh`文件夹中。

#### 1.2 安装公钥

控制台输入

```javascript
$ cd .ssh
$ cat id_rsa.pub >> authorized_keys
$ chmod 600 authorized_keys
$ chmod 700 ~/.ssh
```

即可完成服务器端公钥的绑定

#### 1.3 保存私钥到本地

在`.ssh`文件夹中将公钥文件`pub_rsa.pub`下载到本地，即可在 xshell 等软件中使用。

#### 1.4 设置 SSH 使用密钥验证方式

编辑 /etc/ssh/sshd_config
```javascript
$ nano /etc/ssh/sshd_config
```

文件添加以下内容，

```javascript
RSAAuthentication yes
PubkeyAuthentication yes

ClientAliveInterval 60
ClientAliveCountMax 5

AllowTcpForwarding yes
```

注意其中的几个配置选项
```javascript
PermitRootLogin yes              ####  允许以root身份登录
PasswordAuthentication no        ####  在SSH密钥登入测试完成后再修改此项！
```

重启 SSH 服务，完成配置
```javascript
$ service sshd restart
```

### 2. 在本地生成新的密钥对

再 PUTTY 或 Xshell 的密钥管理中生成自己的密钥对，将`id_rsa.pub`或者用户密钥管理选项中的公钥部分保存或复制下来

#### 2.1 激活密钥对

在 root 目录中新建.ssh 或者随便哪个地方把公钥保存，命名为 id_rsa.pub 或者随便什么名字，然后在控制台输入
```javascript
$ cat id_rsa.pub >> authorized_keys
$ chmod 600 authorized_keys
$ chmod 700 ~/.ssh
```
最后安装上面所介绍的 SSH 服务配置方式激活密钥验证方式即可使用

### 3. 确保新端口在防火墙中放行

1. 关闭防火墙：
`/etc/init.d/iptables stop`
或者
`service iptables stop`

或者在防火墙过滤规则中上增加一条，允许对新增的端口的访问：
vi /etc/sysconfig/iptables

新增一条策略，放通新端口。
例如新端口为12022：
-A INPUT -m state --state NEW -m tcp -p tcp --dport 12022 -j ACCEPT

2. 编辑ssh配置文件
`vim /etc/ssh/sshd_config`

修改#Port 22，去掉#号，端口设置为新端口

3. 重启ssh服务

`systemctl restart sshd`
或者
`/etc/init.d/sshd restart`








