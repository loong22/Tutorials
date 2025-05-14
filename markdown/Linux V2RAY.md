## TCP+HTTP

### 1. 安装命令

github: https://github.com/v2fly/fhs-install-v2ray

```bash

// 安裝執行檔和 .dat 資料檔  安裝和更新 V2Ray
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)

// 只更新 .dat 資料檔  安裝最新發行的 geoip.dat 和 geosite.dat
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh)

// 移除 V2Ray
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh) --remove
```

### 2. 安装后设置开机自启

```cpp
# /etc/systemd/system/v2ray.service.d/10-donot_touch_single_conf.conf
# In case you have a good reason to do so, duplicate this file in the same directory and make your customizes there.
# Or all changes you made will be lost!  # Refer: https://www.freedesktop.org/software/systemd/man/systemd.unit.html
[Service]
ExecStart=
ExecStart=/usr/local/bin/v2ray run -config /usr/local/etc/v2ray/config.json

installed: /usr/local/bin/v2ray
installed: /usr/local/share/v2ray/geoip.dat
installed: /usr/local/share/v2ray/geosite.dat
installed: /usr/local/etc/v2ray/config.json
installed: /var/log/v2ray/
installed: /var/log/v2ray/access.log
installed: /var/log/v2ray/error.log
installed: /etc/systemd/system/v2ray.service
installed: /etc/systemd/system/v2ray@.service
removed: /tmp/tmp.e0xidWJcNm
info: V2Ray v5.14.1 is installed.
You may need to execute a command to remove dependent software: apt purge curl unzip
Please execute the command: systemctl enable v2ray; systemctl start v2ray

// 命令
systemctl enable v2ray
systemctl start v2ray
```

### 3. 设置vmess http配置

```bash
# 文件位置/usr/local/etc/v2ray/config.json
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 25566, #自己配置
            "protocol": "vmess",
            "settings": {
                "clients": [
                    {
                        "id": ""  #自己生成
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "tcpSettings": {
                    "header": {
                        "type": "http",
                        "response": {
                            "version": "1.1",
                            "status": "200",
                            "reason": "OK",
                            "headers": {
                                "Content-Type": [
                                    "application/octet-stream",
                                    "video/mpeg",
                                    "application/x-msdownload",
                                    "text/html",
                                    "application/x-shockwave-flash"
                                ],
                                "Transfer-Encoding": [
                                    "chunked"
                                ],
                                "Connection": [
                                    "keep-alive"
                                ],
                                "Pragma": "no-cache"
                            }
                        }
                    }
                },
                "security": "none"
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}
```

## WS+TLS+NGINX

### 1.安装nginx和v2ray
```sh
sudo apt-get install nginx
#安裝和更新 V2Ray
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh)
#安裝最新發行的 geoip.dat 和 geosite.dat
bash <(curl -L https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh)
```

### 2.配置nginx.conf和v2ray.conf文件

#### 2.1 在/etc/nginx/sites-available/default中，把下面这两句话注释掉(如果nginx打不开)

```sh
#listen 80 default_server;
#listen [::]:80 default_server;
```

#### 2.2 修改nginx.conf文件如下
```sh
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
    worker_connections 768;
    # multi_accept on;
}

http {
    include       mime.types;
    default_type  application/octet-stream;
    
    

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;
    #gzip_vary on;
    
    server {
        listen 443 ssl;
        #listen [::]:443 ssl;
        server_name *.com; #网站地址
        ssl_certificate  /root/*.cer; #证书地址
        ssl_certificate_key /root/*.key; #密钥地址
        ssl_session_timeout 1d;
        ssl_session_cache shared:MozSSL:10m;
        ssl_session_tickets off;
        root /usr/share/nginx/html;

        ssl_protocols         TLSv1.2 TLSv1.3;
        ssl_ciphers           ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;
        ssl_prefer_server_ciphers off;

        location /FDJSKFfhsuda34huhu23 { # 与 V2Ray 配置中的 path 保持一致
            if ($http_upgrade != "websocket") { # WebSocket协商失败时返回404
                return 404;
            }
            proxy_redirect off;
            proxy_pass http://127.0.0.1:1112; # 假设WebSocket监听在环回地址的1112端口上;须与V2ray配置端口一致
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $host;
            # Show real IP in v2ray access.log
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }
    }
    
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

#### 2.3 修改v2ray.conf文件如下

```
#/usr/local/etc/v2ray/config.json
{
  "inbounds": [
    {
      "port": 1112,
      "listen":"127.0.0.1",//只监听 127.0.0.1，避免除本机外的机器探测到开放了 1112 端口
      "protocol": "vmess",
      "settings": {
        "clients": [
          {
            "id": "3921a0ae-4de0-48e1-93c1-5c2cf2729d87",//自定义生成
            "alterId": 0
          }
        ]
      },
      "streamSettings": {
        "network": "ws",
        "wsSettings": {
        "path": "/FDJSKFfhsuda34huhu23"
        }
      }
    }
  ],
  "outbounds": [
    {
      "protocol": "freedom",
      "settings": {}
    }
  ]
}
```

### 3.开机自启v2ray和nginx

```sh
#开机自启
systemctl enable v2ray
systemctl enable nginx
#重启
systemctl restart v2ray
systemctl restart nginx
```