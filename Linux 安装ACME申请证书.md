# Linux 使用 acme.sh 申请 SSL 证书完整指南

## 简介

`acme.sh` 是一个功能强大的开源脚本，用于自动申请和管理 SSL 证书。它支持多种 DNS 提供商的 API，极大简化了证书申请和续期过程。

**参考来源：** 毕世平 - https://shiping.date/82.html

## 准备条件

- 一台具有公网 IP 的 Linux 主机
- 一个域名（建议使用付费域名）
- SSH 客户端工具
- 基本的 Linux 命令行操作能力

## 方法一：Cloudflare Global API

### 1.1 安装 acme.sh 脚本

使用 `root` 用户连接服务器，执行以下命令：

```bash
# 更新系统包并安装依赖
apt update && apt -y install socat

# 安装 acme.sh 脚本
wget -qO- get.acme.sh | bash

# 使环境变量生效
source ~/.bashrc

# 设置默认 CA 为 Let's Encrypt
acme.sh --set-default-ca --server letsencrypt
```

> **注意：** 此方式无需提前创建网页文件，可直接申请证书。

### 1.2 获取 Cloudflare API Key

1. 注册 Cloudflare 账号
2. 将域名 DNS 解析权限转移给 Cloudflare
3. 获取 Global API Key（在 Cloudflare 控制台的 API 部分）

### 1.3 申请证书

设置环境变量并申请证书（以 `yourdomain.com` 为例）：

#### 方式一：DNS 验证（推荐）

```bash
# 设置 Cloudflare API 凭据
export CF_Key="your_global_api_key_here"
export CF_Email="your_cloudflare_email@example.com"

# 申请证书（支持通配符）
acme.sh --issue --dns dns_cf -d yourdomain.com -d www.yourdomain.com -k ec-256
```

> **说明：** Cloudflare 不再支持 `.tk`、`.cf`、`.ml` 等免费域名使用 DNS 验证方式，仅支持付费域名。

#### 方式二：HTTP 验证

```bash
# 确保 80 端口未被占用，需要提前配置 DNS A 记录
acme.sh --issue --standalone -d yourdomain.com -d www.yourdomain.com -k ec-256
```

### 1.4 安装证书

将证书安装到指定目录：

```bash
# 创建证书目录
mkdir -p /root/ssl

# 安装证书文件
acme.sh --installcert -d yourdomain.com -d www.yourdomain.com \
  --fullchain-file /root/ssl/web.crt \
  --key-file /root/ssl/web.key \
  --ecc
```

### 1.5 证书更新

```bash
# 手动强制更新证书
acme.sh --renew -d yourdomain.com -d www.yourdomain.com --force --ecc
```

> **说明：** 证书有效期为 90 天，脚本会每 60 天自动更新。

## 方法二：Cloudflare 局部 API Token

### 2.1 安装 acme.sh 脚本

```bash
# 更新系统并安装依赖
apt update && apt -y install socat

# 安装脚本
wget -qO- get.acme.sh | bash

# 使环境变量生效
source ~/.bashrc

# 设置默认 CA
acme.sh --set-default-ca --server letsencrypt
```

### 2.2 创建 Cloudflare API Token

1. 登录 Cloudflare 控制台
2. 进入 "My Profile" → "API Tokens"
3. 点击 "Create Token"
4. 配置权限：
   - `Zone:DNS:Edit`
   - `Zone:Zone:Read`
5. 区域资源选择：`Include - All zones - 你的账户`

> **重要：** Token 仅显示一次，请务必保存！

### 2.3 获取账户 ID 和区域 ID

在 Cloudflare 域名管理页面右侧 API 区域获取：
- Account ID（必须）
- Zone ID（可选）

### 2.4 申请证书

```bash
# 设置环境变量
export CF_Token="your_api_token_here"
export CF_Account_ID="your_account_id_here"
export CF_Zone_ID="your_zone_id_here"  # 可选

# 申请通配符证书
acme.sh --issue -d example.com -d *.example.com --dns dns_cf -k ec-256
```

### 2.5 安装证书

```bash
# 创建证书目录
mkdir -p /etc/ssl

# 安装证书并设置重载命令
acme.sh --installcert -d example.com -d *.example.com \
  --fullchain-file /etc/ssl/web.crt \
  --key-file /etc/ssl/web.key \
  --ecc \
  --reloadcmd "systemctl restart nginx"
```

### 2.6 证书管理

```bash
# 查看已申请的证书
acme.sh --list

# 手动强制更新 ECC 证书
acme.sh --renew -d example.com -d *.example.com --force --ecc

# 手动强制更新 RSA 证书
acme.sh --renew -d example.com -d *.example.com --force
```

## 方法三：Nginx Webroot 方式

### 3.1 安装 acme.sh

```bash
# 使用个人邮箱安装
curl https://get.acme.sh | sh -s email=your_email@example.com
```

### 3.2 配置 Nginx

创建 Nginx 配置文件：

```bash
# 创建配置文件
vim /etc/nginx/sites-available/example.com
```

配置文件内容：

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    # ACME 验证路径
    location /.well-known/acme-challenge/ {
        root /var/wwwroot/;
        try_files $uri =404;
    }

    # 重定向到 HTTPS
    location / {
        return 301 https://$host$request_uri;
    }
}
```

启用配置：

```bash
# 创建软链接
ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/

# 测试配置
nginx -t

# 重载配置
nginx -s reload
```

### 3.3 申请证书

```bash
# 创建 Web 根目录
mkdir -p /var/wwwroot/.well-known/acme-challenge

# 申请证书
acme.sh --issue -d example.com -d www.example.com --webroot /var/wwwroot
```

## 常用命令参考

```bash
# 查看帮助
acme.sh --help

# 查看版本
acme.sh --version

# 升级脚本
acme.sh --upgrade

# 卸载脚本
acme.sh --uninstall

# 设置自动升级
acme.sh --upgrade --auto-upgrade

# 禁用自动升级
acme.sh --upgrade --auto-upgrade 0
```

## 参考链接

- [acme.sh 官方 GitHub](https://github.com/acmesh-official/acme.sh)
- [acme.sh DNS API 文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)
- [Cloudflare API 文档](https://developers.cloudflare.com/api/)

## 故障排除

### 常见问题

1. **端口占用**：确保 80 端口未被占用（standalone 模式）
2. **DNS 传播**：等待 DNS 记录传播完成
3. **防火墙**：检查防火墙是否允许相关端口
4. **权限问题**：确保以 root 用户执行或具有足够权限

### 调试模式

```bash
# 开启调试模式
acme.sh --issue -d example.com --debug
```