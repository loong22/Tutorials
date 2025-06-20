# Ubuntu

### 1. 在线机器下载并创建索引

```
sudo apt update
sudo apt install apt-rdepends dpkg-dev
```

```
mkdir -p /opt/repo
cd /opt/repo

# 获取所有依赖（递归）
apt-rdepends docker.io docker-compose build-essential \
  | grep -v "^ " \
  | sort -u \
  | while read pkg; do
      if apt-cache show "$pkg" | grep -q "Filename:"; then
        echo "$pkg"
      else
        echo "虚拟包已跳过：$pkg" >&2
      fi
    done > packages.txt
```

```
mkdir debs
cd debs

sudo chown -R _apt:root /opt/repo/debs
sudo chmod -R 755 /opt/repo/debs

# 批量下载
xargs -a ../packages.txt -n 1 apt-get download
```

```
#生成本地 APT 仓库索引
apt-ftparchive packages debs > Packages
gzip -c Packages > Packages.gz
apt-ftparchive release . > Release

# 打包所有内容
tar -czvf repo.tar.gz debs Packages Packages.gz Release
```

## 2. 离线机器建立apt管理

解压：
```bash
tar -xzvf repo.tar.gz -C /opt/repo
```
添加本地源：
```bash
echo "deb [trusted=yes] file:/opt/repo ./" | sudo tee /etc/apt/sources.list.d/offline-docker.list
sudo apt update
```

安装软件：
```bash
sudo apt install docker.io docker-compose build-essential
```

# Centos

### 1. **下载软件包及其依赖**

1. **使用 `yumdownloader` 工具**:
    
    - 首先，你需要在联网的 CentOS 7 系统上安装 `yum-utils` 包，它包含 `yumdownloader` 工具。
        
        `sudo yum install yum-utils`
        
    - 使用 `yumdownloader` 下载所需的软件包及其依赖。例如，要下载 `httpd` 包及其依赖：
        
        `yumdownloader --resolve httpd`
        
        `--resolve` 参数会自动下载 `httpd` 包所需的所有依赖包。
2. **下载所有依赖包**:
    
    - 如果你想下载多个包或某些特定的包，可以分别运行 `yumdownloader` 命令：
        
        `yumdownloader --resolve package-name`
        

### 2. **下载所有软件包及依赖到本地目录**

1. **使用 `reposync` 工具**:
    
    - 你可以使用 `reposync` 下载整个仓库或某个特定的软件包。
    - 首先，安装 `yum-utils`，如果还没有安装：
        
        `sudo yum install yum-utils`
        
    - 使用 `reposync` 下载指定的仓库：
        
        `reposync -r repo-id -p /path/to/local/repo`
        
        其中 `repo-id` 是你要下载的仓库的 ID（如 `base` 或 `updates`），`/path/to/local/repo` 是你希望存储下载文件的本地路径。
2. **将软件包拷贝到离线环境**:
    
    - 将下载的软件包（.rpm 文件）拷贝到 USB 驱动器或其他移动存储设备中。

### 3. **在离线环境中安装软件包**

1. **将软件包复制到离线环境**:
    
    - 将下载的软件包从移动存储设备复制到离线环境中的某个目录，例如 `/mnt/packages`。
2. **安装软件包**:
    
    - 使用 `yum` 工具安装这些软件包：
        
        `sudo yum localinstall /mnt/packages/*.rpm`
        
    - 或者，如果你希望使用 `rpm` 直接安装：
        
        `sudo rpm -ivh /mnt/packages/package-name.rpm`
        

### 4. **创建本地仓库（可选）**

如果你有很多包，创建一个本地仓库会更加高效：

1. **安装 `createrepo` 工具**:
    
    - 在离线环境中，安装 `createrepo` 工具（需要联网环境）：
        
        `sudo yum install createrepo`
        
2. **创建本地仓库**:
    
    - 进入包含所有 RPM 包的目录：
        
        `cd /mnt/packages`
        
    - 运行 `createrepo` 来创建仓库元数据：
        
        `createrepo .`
        
3. **配置 `yum` 使用本地仓库**:
    
    - 在 `/etc/yum.repos.d/` 下创建一个新的 `.repo` 文件，例如 `local.repo`，内容如下：
        
        `[local] name=Local Repository baseurl=file:///mnt/packages/ enabled=1 gpgcheck=0`
        
    - 运行 `yum clean all` 和 `yum repolist` 以刷新缓存并验证本地仓库是否可用。

### 5. 下载到指定路径
`yumdownloader --resolve httpd` 下载的 RPM 包默认保存在当前工作目录。如果你想指定保存位置，可以使用 `-d` 参数。例如：

`yumdownloader --resolve -d /path/to/directory httpd`

这样，所有下载的 RPM 包会保存在你指定的目录中。

