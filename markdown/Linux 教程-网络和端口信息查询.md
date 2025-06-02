### 1. nethogs

**nethogs(推荐)**
查看端口传输速度
```bash
#可以查看端口速率
apt install nethogs

#使用命令
sudo nethogs
```

### 2. netstat
查看端口占用情况
```bash
#查看tcp端口占用
netstat -tln

#如果您想查看端口 80 的状态，您可以使用以下命令：
netstat -tln | grep 80
```

## 1.查看端口占用的程序
在 Linux 中查看端口被哪个程序占用，有多种常用命令和方法。以下是详细的步骤和工具，供你选择使用：

---

### 方法 1：使用 `netstat`
`netstat` 是一个经典的网络状态查看工具，可以显示端口占用情况。

#### 命令
```bash
netstat -tulnp | grep <端口号>
```

#### 示例
假设要查看 8080 端口：
```bash
netstat -tulnp | grep 8080
```

#### 输出解释
输出示例：
```
tcp  0  0  0.0.0.0:8080  0.0.0.0:*  LISTEN  12345/java
```
- `-t`：显示 TCP 端口。
- `-u`：显示 UDP 端口。
- `-l`：仅显示监听状态的端口。
- `-n`：以数字形式显示端口号（而不是服务名）。
- `-p`：显示占用端口的程序 PID 和名称。
- `12345/java`：表示 PID 为 12345 的 Java 进程占用了 8080 端口。

#### 注意
- 需要安装 `net-tools`（Ubuntu: `sudo apt install net-tools`，CentOS: `sudo yum install net-tools`）。
- 必须以 root 或有足够权限的用户运行，否则看不到程序信息。

---

### 方法 2：使用 `ss`
`ss` 是 `netstat` 的现代替代品，速度更快，输出更简洁。

#### 命令
```bash
ss -tuln | grep <端口号>
```

#### 示例
查看 8080 端口：
```bash
ss -tuln | grep 8080
```

#### 输出示例
```
Netid  State  Recv-Q  Send-Q  Local Address:Port  Peer Address:Port
tcp    LISTEN 0       128     0.0.0.0:8080       0.0.0.0:*
```

如果要查看占用程序，加上 `-p`：
```bash
ss -tulnp | grep 8080
```

#### 输出示例
```
tcp    LISTEN 0  128  0.0.0.0:8080  0.0.0.0:*  users:(("java",pid=12345,fd=3))
```
- `-p`：显示进程信息。
- `pid=12345`：占用端口的进程 ID。

#### 优点
- `ss` 默认安装在大多数现代 Linux 发行版中，无需额外安装。

---

### 方法 3：使用 `lsof`
`lsof` 是一个强大的工具，可以列出打开文件的进程，包括网络端口。

#### 命令
```bash
lsof -i:<端口号>
```

#### 示例
查看 8080 端口：
```bash
lsof -i:8080
```

#### 输出示例
```
COMMAND  PID   USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
java    12345 user   3u   IPv4 123456 0t0  TCP *:8080 (LISTEN)
```
- `COMMAND`：进程名。
- `PID`：进程 ID。
- `USER`：运行进程的用户。
- `NAME`：端口信息。

#### 注意
- 需要安装 `lsof`（Ubuntu: `sudo apt install lsof`，CentOS: `sudo yum install lsof`）。
- 需要 root 权限查看其他用户的进程。

---

### 方法 4：使用 `fuser`
`fuser` 可以快速查找占用端口的进程 ID。

#### 命令
```bash
fuser <端口号>/tcp
# 或
fuser <端口号>/udp
```

#### 示例
查看 8080 端口：
```bash
fuser 8080/tcp
```

#### 输出示例
```
8080/tcp: 12345
```
- `12345` 是占用端口的进程 ID。

#### 获取进程详情
结合 `ps` 查看进程信息：
```bash
ps -p 12345 -o comm=
```
输出进程名，例如 `java`。

#### 注意
- 需要安装 `psmisc`（Ubuntu: `sudo apt install psmisc`，CentOS: `sudo yum install psmisc`）。

---

### 方法 5：使用 `nmap`（仅限本地测试）
如果需要检查本地主机的端口占用，可以使用 `nmap`。

#### 命令
```bash
nmap localhost -p <端口号>
```

#### 示例
```bash
nmap localhost -p 8080
```

#### 输出示例
```
PORT     STATE  SERVICE
8080/tcp open   http
```
- `nmap` 不直接显示进程，但可以确认端口是否被占用。
- 需安装：`sudo apt install nmap` 或 `sudo yum install nmap`。

---

### 综合示例：一步到位
如果你想快速找到端口和程序，以下组合很实用：
```bash
ss -tuln | grep 8080 || lsof -i:8080 || fuser 8080/tcp
```
然后根据 PID 用 `ps` 查看详情：
```bash
ps -aux | grep 12345
```

---

### 推荐选择
- **首选 `ss`**：现代、快速，默认可用。
- **次选 `lsof`**：详细输出，适合复杂场景。
- **快速排查用 `fuser`**：简单直接。

### 注意事项
1. **权限问题**  
   普通用户可能看不到其他用户占用的端口，建议用 `sudo` 提升权限。
2. **端口类型**  
   确认是 TCP 还是 UDP，分别用 `/tcp` 或 `/udp`（`fuser`）或调整命令选项。
3. **动态端口**  
   如果端口是动态分配的，可能需要先通过服务日志或配置确认具体端口号。
