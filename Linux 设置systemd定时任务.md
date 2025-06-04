## 使用systemd实现
下面是使用 `systemd` 定时器 (`.timer`) 配置 3 个示例的定时任务，每个任务都运行一个 `.sh` 脚本：

### **1. 创建 `systemd` 服务文件**
每个定时任务需要一个对应的 `service` 文件，比如：
```ini
[Unit]
Description=Run a script periodically

[Service]
ExecStart=/path/to/script.sh
Type=simple
```

### **2. 创建定时任务**
#### **任务 1：每分钟执行一次**
- **创建 `mytask1.service`**
  ```bash
  sudo nano /etc/systemd/system/mytask1.service
  ```
  **内容：**
  ```ini
  [Unit]
  Description=Run script1 every minute

  [Service]
  ExecStart=/bin/bash -c '/root/example.sh >> /root/example.log 2>&1'
  Type=simple
  ```
  ExecStart 不能直接用 >>，因为 systemd 解析 ExecStart 时不支持 Shell 的重定向符号。

  解决方法是用 /bin/bash -c 让 bash 解析命令，并正确重定向标准输出和错误输出
  
##### 其他改进（可选）
如果你希望 systemd 直接管理日志，而不是手动写入文件，可以使用：
```
StandardOutput=append:/root/example.log
StandardError=append:/root/example.log
```
然后 ExecStart 只需：
```
ExecStart=/root/example.sh
```
这样 systemd 会自动把日志写入 /root/example.log，并且可以用 journalctl -u mytask1.service 查看日志。


- **创建 `mytask1.timer`**
  ```bash
  sudo nano /etc/systemd/system/mytask1.timer
  ```
  **内容：**
  ```ini
  [Unit]
  Description=Run script1 every minute

  [Timer]
  OnBootSec=1min
  OnUnitActiveSec=1min
  Unit=mytask1.service

  [Install]
  WantedBy=timers.target
  ```

#### **任务 2：每 8 小时执行一次**
- **创建 `mytask2.service`**
  ```bash
  sudo nano /etc/systemd/system/mytask2.service
  ```
  **内容：**
  ```ini
  [Unit]
  Description=Run script2 every 8 hours

  [Service]
  ExecStart=/bin/bash -c '/root/example.sh >> /root/example.log 2>&1'
  Type=simple
  ```

- **创建 `mytask2.timer`**
  ```bash
  sudo nano /etc/systemd/system/mytask2.timer
  ```
  **内容：**
  ```ini
  [Unit]
  Description=Run script2 every 8 hours

  [Timer]
  OnBootSec=10min
  OnUnitActiveSec=8h
  Unit=mytask2.service

  [Install]
  WantedBy=timers.target
  ```

#### **任务 3：每 15 天执行一次**
- **创建 `mytask3.service`**
  ```bash
  sudo nano /etc/systemd/system/mytask3.service
  ```
  **内容：**
  ```ini
  [Unit]
  Description=Run script3 every 15 days

  [Service]
  ExecStart=/bin/bash -c '/root/example.sh >> /root/example.log 2>&1'
  Type=simple
  ```

- **创建 `mytask3.timer`**
  ```bash
  sudo nano /etc/systemd/system/mytask3.timer
  ```
  **内容：**
  ```ini
  [Unit]
  Description=Run script3 every 15 days

  [Timer]
  OnBootSec=30min
  OnUnitActiveSec=15d
  Unit=mytask3.service

  [Install]
  WantedBy=timers.target
  ```


### **3. 重新加载 `systemd` 并启用定时器**
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now mytask1.timer
sudo systemctl enable --now mytask2.timer
sudo systemctl enable --now mytask3.timer
```

### **4. 检查任务是否运行**
查看定时器状态：
```bash
systemctl list-timers --all
```
检查特定任务：
```bash
systemctl status mytask1.timer
systemctl status mytask2.timer
systemctl status mytask3.timer
```

查看日志：
```bash
journalctl -u mytask1.service --no-pager
journalctl -u mytask2.service --no-pager
journalctl -u mytask3.service --no-pager
```

这样，3 个 `systemd` 定时任务就配置好了：
- **任务 1**：每 **1 分钟** 执行 `script1.sh`
- **任务 2**：每 **8 小时** 执行 `script2.sh`
- **任务 3**：每 **15 天** 执行 `script3.sh`


## 如果修改了mytask1.service或mytask1.timer

需要重新加载新的配置

### **1. 重新加载 `systemd` 配置**
```bash
sudo systemctl daemon-reload
```
这一步让 `systemd` 重新读取修改后的 `.service` 和 `.timer` 文件。

### **2. 重新启动 `.timer`**
```bash
sudo systemctl restart mytask1.timer
```
这样 `timer` 会重新计算下次执行时间，并使用新的 `service` 文件。

### **3. （可选）检查定时器状态**
你可以运行以下命令来确认 `timer` 是否正确启动：
```bash
systemctl list-timers --all | grep mytask1
```
如果想检查日志：
```bash
journalctl -u mytask1.service --no-pager
```

### **4. （可选）如果 `service` 也要手动执行**
如果你想立刻运行一次脚本，而不是等 `timer` 触发：
```bash
sudo systemctl start mytask1.service
```
然后查看状态：
```bash
sudo systemctl status mytask1.service
```

