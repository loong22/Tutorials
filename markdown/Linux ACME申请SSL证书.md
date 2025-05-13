# 转载：acme.sh脚本申请cloudflare的证书

备注：本文是将原作者的两种申请cloudflare证书的方式合在一起，即用global API和局部 API两种。 

- 作者: 毕世平 https://shiping.date/82.html 

前言：`acme.sh`是一个非常好用的用来申请证书的脚本，它开源在Github，它极大地降低了申请证书的难度，支持使用cloudflare api等众多api来申请证书。

本文主要介绍使用此脚本来申请ssl证书，给你的http请求加把锁，具体会使用`cloudflare api`来介绍。

## 准备条件：

- 一台被分配了公网IP的主机

- 一个域名(建议购买收费域名)

- 电脑ssh客户端

- 能解决遇到问题的个体


## 1. Global API申请证书部份



### 1.1、安装脚本文件

以`root`用户ssh连接到主机，使用下面命令安装脚本：

```
###注意: 此方式申请ssl证书不需要提前创建网页文件, 可以直接申请

apt update && apt -y install socat //更新源并安装socat

wget -qO- get.acme.sh | bash //安装此脚本

source ~/.bashrc //让别名生效，此后无论在哪里直接使用acme.sh，不用输绝对路径

# 由于最新acme.sh脚本默认ca变成了zerossl，现执行下面命令修改脚本默认ca为letsencrypt
acme.sh --set-default-ca --server letsencrypt
```

### 1.2、配置证书

#### 1.2.1 获取Cloudflare api key

注册好cloudflare账号，把域名dns的解析权限交给cloudflare处理，这样以后只需要在cloudflare这里配置解析记录。按照图示获取`cloudflare api key`。

#### 1.2.2 申请证书（二选一）

这里假定你的域名是`yourdomain.com`，执行下面命令申请证书：

#### 1.2.2.1 dns方式验证

```
export CF_Key="slfjksjffjfhfhkjhfksjf" //此处替换成你自己的Key
export CF_Email="yourcloudflare@gmail.com" //此处填写你给Cloudflare绑定的邮箱账号

acme.sh --issue --dns dns_cf -d yourdomain.com -d www.yourdomain.com -k ec-256
```

**说明**：Cloudflare现已不支持`.tk .cf .ml`等免费申请来的域名后缀使用此dns验证方式，付费域名才受支持，免费域名使用下面2.2.2方式。脚本申请到的证书和key都放在`~/.acme.sh/yourdomain.com_ecc`目录下。

#### 1.2.2.2 http方式验证

执行下面命令前请保证你80端口没有被别的程序使用，如果有，kill掉，执行下面命令前需要你先在cloudflare dns那里添加好A记录，可以开启cloudflare cdn（cdn对此http方式验证无影响）：

```
acme.sh --issue --standalone -d yourdomain.com -d www.yourdomain.com -k ec-256
```

**说明**：跟上面一样，脚本申请到的证书放在`~/.acme.sh/yourdomain.com_ecc`目录下

#### 1.2.3 安装证书到指定位置

假定linux主机里已有`/root/ssl`目录，现在要把证书和key安装到此目录下，那么执行下面的命令即可：

```
acme.sh --installcert -d yourdomain.com -d www.yourdomain.com --fullchain-file /root/ssl/web.crt --key-file /root/ssl/web.key --ecc
```

### 1.3、证书更新

从letscncrypt申请到的证书有效期是90天，但脚本会每60天会对证书进行更新，后续可能会缩短这个时间，都是自动的。

```
# 手动强制更新证书
acme.sh --renew -d yourdomain.com -d www.yourdomain.com --force --ecc
```



## 2. 局部API部份

本文主要以`Debian10`为例，介绍使用新的`cloudflare api`令牌来申请证书，免费域名已不受`cloudflare`此种方式来申请证书，请使用付费域名。

### 2.1、安装配置acme.sh脚本

以`root`用户ssh登陆到主机，使用下面命令安装配置脚本：

```
# 更新源并安装socat
apt update && apt -y install socat

# 安装脚本
wget -qO- get.acme.sh | bash

# 让脚本在.bashrc文件追加的一行环境变量生效，以后无论在哪里直接使用acme.sh，不用输绝对路径
source ~/.bashrc

# 由于最新acme.sh脚本默认ca变成了zerossl，现执行下面命令修改脚本默认ca为letsencrypt
acme.sh --set-default-ca --server letsencrypt
```

### 2.2、配置Cloudflare局部令牌

#### 2.2.1 创建令牌

根据`acme.sh`脚本作者提供的文档：[使用新的cloudflare api令牌](https://github.com/acmesh-official/acme.sh/wiki/dnsapi#using-the-new-cloudflare-api-token-you-will-get-this-after-normal-login-and--scroll-down-on-dashboard-and-copy-credentials)，需要新创建此令牌方可使用：


依照上述图片设置即可，添加`区域-DNS-编辑`和`区域-区域-读取`权限，区域资源里面选择`包括-账户的所有区域-你的账户`。

配置好以后，点击`创建令牌`，注意此令牌`token`只会**出现一次，切记保存，切记保存，切记保存！！！**

#### 2.2.2 获取账户ID和区域ID

根据`acme.sh`文档，申请证书可能还需要提供两个ID，其中账户ID必须，区域ID可有可无，根据下图方法获取此两个ID：

下拉，找到API区域：

至此，即准备好了申请证书需要的材料：

```
# 上面第一步创建的令牌，即为token
CF_Token="xxxxxxx"

# 根据上图分别获取账户ID和区域ID
CF_Account_ID="aaaaaaaa"
CF_Zone_ID="bbbbbbbbb"   //此项非必须
```

### 2.3、申请配置证书

#### 2.3.1 申请证书

假定你的域名是`example.com`，执行下面命令申请证书，非`root`用户也可执行：

```
# 执行此命令设置变量的值，acme.sh脚本执行过程会读取
export CF_Token="xxxxxxx"
export CF_Account_ID="aaaaaaa"
export CF_Zone_ID="bbbbbbbb"   //此项非必须，上面两项需要提供

# 申请证书
acme.sh --issue -d example.com -d *.example.com --dns dns_cf -k ec-256
```

#### 2.3.2 安装证书到指定位置

假定linux主机里已有`/etc/ssl`目录，现在要把证书和key安装到此目录下，那么执行下面的命令即可：

```
acme.sh --installcert -d example.com -d *.example.com --fullchain-file /etc/ssl/web.crt --key-file /etc/ssl/web.key --ecc

# 用--reloadcmd指定安装证书后的命令
acme.sh --installcert -d example.com -d *.example.com --fullchain-file /etc/ssl/web.crt --key-file /etc/ssl/web.key --ecc --reloadcmd "systemctl restart webserver"
```

**说明**：脚本更新完证书后，会自动依据上面指定的绝对路径将证书和key安装到指定位置，并根据`--reloadcmd`执行相应的操作。

#### 2.3.3 证书更新

从`letsencrypt`申请到的证书有效期是90天，脚本每60天会对证书进行更新，你也可以手动强制更新：

```
# 查询域名申请证书信息
acme.sh --list

# 手动强制更新证书
acme.sh --renew -d example.com -d *.example.com --force --ecc
acme.sh --renew -d example.com -d *.example.com --force //非ECC证书使用此命令
```

### 参考链接：

- [acme.sh DNS文档](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)
- [acme.sh在github开源地址](https://github.com/acmesh-official/acme.sh)


# 其他方式
### 1. 安装`acme.sh`
```
#替换为个人邮箱
curl https://get.acme.sh | sh -s email=example@example.com  
```

### 2. 创建nginx配置文件
```
#在/etc/nginx/sites-available路径下创建example.com文件，文件内容见示例
vim /etc/nginx/sites-available/example.com
```

example.com内容示例如下：

```
server {
    listen 80;
    server_name translate.123.com;

    #确保这个路径存在: /var/wwwroot/.well-known/acme-challenge
    location /.well-known/acme-challenge/ {
        root /var/wwwroot/;
        try_files $uri =404;
    }

    location / {
        rewrite ^ https://$http_host$request_uri? permanent;
        #return 301 https://$host$request_uri;
    }

    #error_log /var/log/nginx/translate.123.com.error.log;
    #access_log /var/log/nginx/translate.123.com.access.log;
}

```

把写好的配置文件链接到/etc/nginx/sites-enabled路径下

```
ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled
```

### 3. 申请SSL证书
```
#创建web根目录
#假设您要为 example.com 和 www.example.com 申请证书，首先需要在您的 web 根目录下创建 .well-known/acme-challenge 目录，并确保该目录可以通过 HTTP 访问。一般来说，web 根目录是 /var/www/html 或 /usr/share/nginx/html。

mkdir -p /var/www/html/.well-known/acme-challenge


#--force是强制更新，第一次申请可以不用添加
acme.sh --issue -d www.example.com --webroot /var/wwwroot

acme.sh  --issue  -d example.com  --webroot /var/wwwroot --force
```

### 4. 重启nginx
```
#测试配置文件是否通过
nginx -t

#重启nginx服务
nginx -s reload
```