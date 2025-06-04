在 Linux 中修改 hostname 后，通常不需要重启系统即可生效，但具体取决于你使用的修改方式以及系统配置。以下是常见的修改 hostname 的方法及生效方式：

### 方法 1：使用 `hostnamectl` 命令（推荐）
这是现代 Linux 系统（如使用 systemd 的发行版，例如 Ubuntu 16.04+、CentOS 7+ 等）推荐的方式。
1. 修改 hostname：
   ```bash
   sudo hostnamectl set-hostname 新主机名
   ```
   例如：
   ```bash
   sudo hostnamectl set-hostname mynewhost
   ```
2. 检查是否生效：
   ```bash
   hostname
   ```
   或：
   ```bash
   hostnamectl
   ```

**生效方式**：使用 `hostnamectl` 修改后，hostname 会立即生效，无需重启系统。它会同时更新静态 hostname（持久保存）和运行时的 hostname。

---

### 方法 2：直接修改 `/etc/hostname` 文件
1. 编辑文件：
   ```bash
   sudo nano /etc/hostname
   ```
   将文件中的旧主机名替换为新主机名，例如：
   ```
   mynewhost
   ```
   保存并退出。
2. 使修改生效：
   - **临时生效**：运行以下命令立即更新当前会话的 hostname：
     ```bash
     sudo hostname mynewhost
     ```
     但这种方式只在当前会话有效，重启后会恢复到 `/etc/hostname` 中的值。
   - **持久生效**：修改 `/etc/hostname` 后，通常需要重启系统才能完全生效。不过，你也可以通过以下命令避免重启：
     ```bash
     sudo systemctl restart systemd-hostnamed
     ```

---

### 方法 3：修改 `/etc/hosts` 文件（可选）
如果你的系统中 hostname 和 IP 地址有映射关系（如 `127.0.0.1 localhost myoldhost`），需要同步更新 `/etc/hosts`：
1. 编辑文件：
   ```bash
   sudo nano /etc/hosts
   ```
   将旧主机名替换为新主机名，例如：
   ```
   127.0.0.1   localhost mynewhost
   ```
2. 保存后无需重启，网络相关服务会自动识别。

---

### 是否必须重启？
- **不需要重启的情况**：
  - 使用 `hostnamectl set-hostname` 修改后立即生效。
  - 手动修改 `/etc/hostname` 并运行 `sudo hostname 新主机名` 或重启 `systemd-hostnamed` 服务。
- **需要重启的情况**：
  - 如果不手动更新运行时 hostname，且某些服务（如网络服务）依赖旧 hostname，可能需要重启系统或相关服务（如 `sudo systemctl restart networking`）。

---

### 验证修改
修改完成后，可以通过以下命令确认：
```bash
hostname
```
或：
```bash
uname -n
```

### 注意事项
- 如果你的系统运行某些依赖 hostname 的服务（如数据库、Web 服务器等），修改 hostname 后可能需要重启这些服务以确保它们使用新的 hostname。
- 在集群或分布式环境中，确保修改 hostname 后通知其他节点（如果有依赖关系）。

总结：大多数情况下，通过 `hostnamectl` 或手动更新运行时 hostname，你可以避免重启系统。