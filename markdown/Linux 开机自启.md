### 1. 配置 Systemd 服务(以Aria2示例)

1 创建一个新的 Systemd 服务文件：
```
sudo nano /etc/systemd/system/aria2.service
```

2 添加以下内容：
```
[Unit]
Description=Aria2c download manager
After=network.target

[Service]
User=yourusername
ExecStart=/usr/bin/aria2c --conf-path=/home/yourusername/.aria2/aria2.conf
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

请将 yourusername 替换为您的实际用户名。

3 重新加载 Systemd 配置：
```
sudo systemctl daemon-reload
```

4 启动 Aria2 服务：
```
sudo systemctl start aria2
```

5 设置 Aria2 服务开机自启：
```
sudo systemctl enable aria2
```