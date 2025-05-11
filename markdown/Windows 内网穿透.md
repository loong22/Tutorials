### 1. 测试
1.首先要有一台中转用的VPS
先用root用户登录VPS，编辑`/etc/ssh/sshd_config`文件，
增加下面4项
```
GatewayPorts yes 
AllowTcpForwarding yes

ClientAliveInterval 60
ClientAliveCountMax 5
```
然后重启
```
ssh service ssh restart
```
2.在windows机器上执行如下ssh语句
``` 
ssh -N -R 33389:localhost:3389 user@vpsIP -p PORT -o ServerAliveInterval=60 -o ServerAliveCountMax=3
```

-N：不执行远程命令，只建立隧道。
-R 33389:localhost:3389：将远程服务器 33389 端口映射到本地 3389 端口。
PORT: SSH端口

这相当于连接VPS后，把所有对VPS:33389端口的访问，都转发到本机3389端口，也就是windows远程桌面的默认端口。

3.使用其他设备打开远程桌面进行连接

连接地址改成vpsIP:33389
用户名密码为Windows机器的用户名和密码


### 2.添加开机自启并防止掉线

### !!!这个方案不稳定,不建议使用

### !!!推荐使用frp(需要公网ip)或者ngrok(不需要公网ip)


需要安装Mobaxterm软件: 
https://mobaxterm.mobatek.net/download.html

激活脚本:
https://github.com/flygon2018/MobaXterm-keygen


在MobaXterm的tunneling中添加Tunneling

1.选择New SSH tunnel, 选择Remote port forwarding

2.填写Local Server和 Local port --- 本地的服务器和端口

3.填写SSH server, SSH Login, SSH port --- 服务器的ip, 用户名, 端口

4.填写Forwarded port --- 转发到服务器的端口

5.点击Save, 然后在列表中选择钥匙图标填写服务器密码或者私钥, 选择autostart和autoconnect打开

6.创建 ssh_keepalive.ps1, 内容如下:
```
$exePath = "C:\Program Files (x86)\Mobatek\MobaXterm\MobaXterm.exe"  # 替换成你的实际路径
$processName = "MobaXterm"
$restartDelaySeconds = 3

function Start-MobaXterm {
    Write-Output "$(Get-Date) - 启动 MobaXterm"
    Start-Process -FilePath $exePath
}

while ($true) {
    $proc = Get-Process -Name $processName -ErrorAction SilentlyContinue
    if (-not $proc) {
        Start-MobaXterm
    }
    Start-Sleep -Seconds $restartDelaySeconds
}

```

使用 任务计划程序（Task Scheduler） 设置开机自启

```

步骤：

1. 快捷键 Win + R，输入 taskschd.msc，回车打开“任务计划程序”。


2. 在左侧任务计划程序库中，右键选择 “创建基本任务”。


3. 名称 随意填写，如 SSH反向代理，点击 “下一步”。


4. 触发器 选择 “计算机启动时”，点击 “下一步”。


5. 操作 选择 “启动程序”，点击 “下一步”。


6. 在 “程序或脚本” 处填写 powershell.exe。


7. 在 “添加参数” 处填写：

-WindowStyle Hidden -ExecutionPolicy Bypass -File "C:\path\to\ssh_keepalive.ps1"

（请替换 C:\path\to\ssh_keepalive.ps1 为你的 PowerShell 脚本路径）


8. 点击 “下一步”，然后 勾选“使用最高权限运行”（确保脚本可以运行）。


9. 完成后右键任务 -> 属性 -> 设置 -> 选中“即使用户未登录也运行”。


10. 确保 “运行任务时使用以下用户帐户” 选择的是 SYSTEM 或管理员用户。
```
