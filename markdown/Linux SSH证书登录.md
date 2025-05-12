
### 1. 在服务器上生成新的密钥对

#### 1.1 生成密钥

服务器终端输入
```sh
ssh-keygen -t rsa -b 4096 #<== 建立密钥对
```

输出内容
```bash
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

```bash
cd .ssh
cat id_rsa.pub >> authorized_keys
chmod 600 authorized_keys
chmod 700 ~/.ssh
```

即可完成服务器端公钥的绑定

#### 1.3 保存私钥到本地

在`.ssh`文件夹中将私钥文件`id_rsa`下载到本地，即可在 xshell 等软件中使用。

#### 1.4 设置 SSH 使用密钥验证方式

编辑 /etc/ssh/sshd_config
```bash
nano /etc/ssh/sshd_config
```

文件添加以下内容，

```bash
RSAAuthentication yes
PubkeyAuthentication yes

ClientAliveInterval 60
ClientAliveCountMax 5

AllowTcpForwarding yes

PermitRootLogin yes              ####  允许以root身份登录
```

重启SSH服务，完成配置
```bash
#重启
service sshd restart
#或
systemctl restart sshd
#或
/etc/init.d/sshd restart
```

#### 1.5 测试使用密钥成功登录后关闭密码登录选项, 然后再重启SSH服务

编辑 /etc/ssh/sshd_config
```bash
nano /etc/ssh/sshd_config
```

文件添加以下内容，
```bash
PasswordAuthentication no        ####  在SSH密钥登入测试完成后再修改此项！
```

重启SSH服务，完成配置
```bash
#重启
service sshd restart
#或
systemctl restart sshd
#或
/etc/init.d/sshd restart
```