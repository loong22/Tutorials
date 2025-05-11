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