要使用 AWS API 将 Cloudflare R2 存储挂载到 Linux 目录下，实际上是利用 Cloudflare R2 与 AWS S3 API 的兼容性，通过工具如 `s3fs` 来实现挂载。以下是具体步骤：

### 前提条件
1. **Cloudflare R2 账号**：你需要有一个 Cloudflare 账号，并已创建 R2 存储桶。
2. **API 凭证**：从 Cloudflare R2 获取访问密钥 ID（Access Key ID）和机密访问密钥（Secret Access Key）。
3. **Linux 系统**：确保你的 Linux 系统已安装必要的工具（如 `s3fs`）。
4. **AWS CLI**（可选）：虽然不直接用于挂载，但可以用来测试 R2 的 S3 兼容性。

---

### 步骤

#### 1. 安装 `s3fs`
`s3fs` 是一个基于 FUSE 的文件系统工具，可以将 S3 兼容的存储（如 R2）挂载到本地目录。

在 Ubuntu/Debian 系统上：
```bash
sudo apt update
sudo apt install s3fs
```

在 CentOS/RHEL 系统上：
```bash
sudo yum install epel-release
sudo yum install s3fs-fuse
```

#### 2. 获取 Cloudflare R2 的凭证
1. 登录 Cloudflare 仪表盘。
2. 进入 R2 页面，创建一个存储桶（例如 `my-bucket`）。
3. 在 R2 的“概述”页面，点击“管理 R2 API 令牌”。
4. 创建一个新的 API 令牌，选择“对象读写”权限，并记录下：
   - **访问密钥 ID**（Access Key ID）
   - **机密访问密钥**（Secret Access Key）
   - **存储桶的 S3 API 端点**（例如 `https://<account-id>.r2.cloudflarestorage.com`）

#### 3. 配置凭证文件
将 R2 的访问密钥写入本地凭证文件：
```bash
echo "ACCESS_KEY_ID:SECRET_ACCESS_KEY" > ~/.passwd-s3fs
```
替换 `ACCESS_KEY_ID` 和 `SECRET_ACCESS_KEY` 为你从 R2 获取的值。

设置文件权限：
```bash
chmod 600 ~/.passwd-s3fs
```

#### 4. 创建挂载点
创建一个本地目录作为挂载点，例如：
```bash
mkdir /mnt/r2
```

#### 5. 挂载 R2 存储桶
使用 `s3fs` 命令将 R2 存储桶挂载到本地目录：
```bash
s3fs my-bucket /mnt/r2 -o passwd_file=~/.passwd-s3fs -o url="https://<account-id>.r2.cloudflarestorage.com" -o use_path_request_style
```
- `my-bucket`：替换为你的 R2 存储桶名称。
- `/mnt/r2`：挂载点目录。
- `-o url`：替换为你的 R2 S3 API 端点。
- `-o use_path_request_style`：确保使用路径风格的请求（R2 需要此选项）。

#### 6. 验证挂载
挂载成功后，进入挂载目录：
```bash
cd /mnt/r2
ls
```
如果存储桶中有文件，你应该能看到它们。如果是空的，可以尝试上传文件测试：
```bash
echo "Hello R2" > test.txt
ls
```
然后在 Cloudflare R2 仪表盘中检查是否同步。

#### 7. 设置开机自动挂载（可选）
编辑 `/etc/fstab`，添加以下一行：
```
s3fs#my-bucket /mnt/r2 fuse _netdev,allow_other,passwd_file=/home/user/.passwd-s3fs,url=https://<account-id>.r2.cloudflarestorage.com,use_path_request_style 0 0
```
- 替换 `my-bucket` 和 `<account-id>`。
- 确保路径和用户权限正确。

然后测试挂载：
```bash
mount -a
```

---

### 注意事项
1. **性能**：`s3fs` 的性能可能不如本地文件系统，适合轻量级使用。
2. **安全性**：确保凭证文件（如 `~/.passwd-s3fs`）权限严格，避免泄露。
3. **替代工具**：如果 `s3fs` 不满足需求，可以尝试 `rclone`（支持挂载但更轻量）或直接用 AWS SDK 编写脚本操作 R2。

### 使用 AWS CLI 测试（可选）
如果你想用 AWS CLI 验证 R2 的兼容性：
1. 安装 AWS CLI：
   ```bash
   pip install awscli
   ```
2. 配置 AWS CLI：
   ```bash
   aws configure
   ```
   输入 R2 的 `Access Key ID`、`Secret Access Key`，地区填 `auto`，端点填 R2 的 S3 URL。
3. 列出存储桶：
   ```bash
   aws --endpoint-url https://<account-id>.r2.cloudflarestorage.com s3 ls
   ```

---
