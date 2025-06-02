# Ubuntu

### 方法 1: 手动下载所有依赖

1. **列出软件包的依赖**：
   在有网络的环境中，你可以使用 `apt-cache` 或 `apt-rdepends` 来列出软件包的所有依赖。

   ```bash
   apt-cache depends <package-name>
   ```

   或者，使用 `apt-rdepends` 来查看所有递归依赖：

   ```bash
   apt-rdepends <package-name>
   ```

2. **下载依赖包**：
   使用 `apt-get download` 或 `apt` 命令分别下载每个依赖包及其版本。例如：

   ```bash
   apt-get download <dependency-package>
   ```

   确保你下载所有列出的依赖包。

### 方法 2: 使用 `apt-get` 下载软件包及其所有依赖

1. **创建一个下载目录**：

   ```bash
   mkdir ~/packages
   cd ~/packages
   ```

2. **使用 `apt-get` 下载软件包及其所有依赖**：

   使用 `apt-get` 的 `--download-only` 和 `--reinstall` 选项，可以尝试下载包及其所有依赖：

   ```bash
   sudo apt-get install --download-only <package-name>
   ```

   这将下载软件包及其所有依赖到 `/var/cache/apt/archives/` 目录。

3. **复制下载的 `.deb` 文件**：

   将 `/var/cache/apt/archives/` 目录下的 `.deb` 文件复制到你的下载目录：

   ```bash
   cp /var/cache/apt/archives/*.deb ~/packages/
   ```

### 方法 3: 使用 `apt-offline`

`apt-offline` 是一个方便的工具，用于在没有网络的环境中处理 APT 包管理器的下载和安装：

1. **在在线环境中安装 `apt-offline`**：

   ```bash
   sudo apt-get install apt-offline
   ```

2. **生成一个签名的包列表**：

   在离线环境中，生成一个签名的包列表（通常是一个 `.sig` 文件）：

   ```bash
   apt-offline set ~/packages/apt-offline.sig
   ```

3. **在有网络的环境中下载包**：

   将生成的 `.sig` 文件拷贝到有网络的机器上，并使用 `apt-offline` 下载所需的包：

   ```bash
   sudo apt-offline get ~/packages/apt-offline.zip
   ```

4. **将下载的包转移到离线环境**：

   将生成的 `.zip` 文件拷贝回离线环境，并使用 `apt-offline` 安装包：

   ```bash
   sudo apt-offline install ~/packages/apt-offline.zip
   ```

### 方法 4: 使用 `aptitude`

`aptitude` 是另一个可以帮助下载依赖的工具。首先安装 `aptitude`：

```bash
sudo apt-get install aptitude
```

然后，你可以使用 `aptitude` 来处理包和依赖：

1. **下载软件包及其所有依赖**：

   ```bash
   sudo aptitude download <package-name>
   ```

   这将下载软件包及其所有依赖到当前目录。

### 总结

在离线环境中安装软件包时，确保你下载了所有必需的依赖包是至关重要的。通过手动下载依赖、使用 `apt-get` 或 `apt-offline`，你可以有效地管理和安装软件包及其依赖。在有网络的环境中，确保将所有 `.deb` 文件转移到离线系统并使用 `dpkg` 进行安装。


## 1. 建立apt管理(基于方法2)

是的，在方法 2 中下载的软件包可以在离线环境中使用 `dpkg` 来安装。具体来说，下面是详细的步骤：

### 1.1 **准备下载的软件包**

首先，确保你已经在有网络的环境中下载了所需的 `.deb` 文件，并将它们转移到离线环境中。假设你已经将这些 `.deb` 文件放在 `/home/user/packages/` 目录下。

### 1.2 **安装软件包**

在离线环境中，使用 `dpkg` 命令安装这些软件包：

```bash
sudo dpkg -i /home/user/packages/*.deb
```

### 1.3 **处理依赖问题**

安装软件包时，`dpkg` 可能会报告缺失的依赖项。如果有依赖问题，你可以使用以下命令解决它们：

#### **a. 配置尚未配置的包**

```bash
sudo dpkg --configure -a
```

#### **b. 尝试修复依赖问题**

如果 `dpkg` 报告依赖问题，尝试手动下载缺失的依赖包并安装，或者：

```bash
sudo apt-get install -f
```

但是，`apt-get` 命令在完全离线的情况下可能不可用。如果 `apt-get` 无法运行，你需要确保所有的依赖包都已经手动下载并安装。你可以使用 `dpkg` 安装这些依赖包：

```bash
sudo dpkg -i /path/to/dependency-package.deb
```

### 1.4 **验证安装**

安装完成后，验证软件包是否正确安装并检查是否有任何问题：

```bash
dpkg -l | grep <package-name>
```

### 1.5 **创建本地仓库（可选）**

如果你需要更方便地管理离线软件包，可以创建一个本地仓库，如下所示：

#### **a. 创建本地仓库目录**

```bash
mkdir -p /home/user/local-repo
```

#### **b. 移动包到本地仓库目录**

```bash
mv /home/user/packages/*.deb /home/user/local-repo/
```

#### **c. 生成包索引**

```bash
cd /home/user/local-repo
dpkg-scanpackages . /dev/null | gzip -9c > Packages.gz
```

#### **d. 添加本地仓库到 APT 源列表**

编辑 `/etc/apt/sources.list` 文件，添加以下行：

```bash
deb [trusted=yes] file:/home/user/local-repo ./
```

然后更新 APT 包列表：

```bash
sudo apt-get update
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

