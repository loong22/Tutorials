
### 1. 连接VPS
```
(省略)
```

### 2. 修改ssh登录端口
```
apt-get update

#确保新端口在防火墙中放行
（1）关闭防火墙：
/etc/init.d/iptables stop
或者
service iptables stop
或者在防火墙过滤规则中上增加一条，允许对新增的端口的访问：
vi /etc/sysconfig/iptables
新增一条策略，放通新端口。
例如新端口为12022：
-A INPUT -m state --state NEW -m tcp -p tcp --dport 12022 -j ACCEPT
（2）编辑ssh配置文件
vim /etc/ssh/sshd_config
修改#Port 22，去掉#号，端口设置为新端口
（3）重启ssh服务
systemctl restart sshd
或者
/etc/init.d/sshd restart
```

### 3.防止暴力破解
```
#防止暴力破解
（1）编写脚本定期将登录失败的IP添加进入黑名单中
touch ssh.sh
chmod +x ssh.sh
vim ssh.sh
（2）ssh.sh内容如下：
#!/bin/bash
#输入三次错误密码自动封禁ip
iplist=$(/bin/lastb |awk '{print $3}'|sort|uniq -c|awk '{if ($1>3) print $2}')
#追加到黑名单并清空登录日志
for ip in ${iplist}
do
echo ALL: ${ip} >> /etc/hosts.deny
echo > /var/log/btmp
done
（3）设置定时执行
#编辑crontab
crontab -e
#添加定时脚本命令，每分钟执行一次
0 */1 * * * sh /root/ssh.sh
```

### 4.启动TCP加速算法
```
#启动BBR加速算法
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```

### 5.安装XUI
```
#安装X-UI For English Users,please use the following command to install English supported version:

bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install_en.sh)
```

```
# install command  指定版本
bash <(curl -Ls https://raw.githubusercontent.com/FranzKafkaYu/x-ui/master/install.sh) 0.3.4.4
```

### 6.流媒体检测
```
bash <(curl -L -s check.unlock.media) -M 4
```

### 7.设置流媒体转发落地
```
#面板设置->Xray相关设置
#按需修改，示例如下
{
    "api": {
      "services": [
        "HandlerService",
        "LoggerService",
        "StatsService"
      ],
      "tag": "api"
    },
    "inbounds": [
      {
        "listen": "127.0.0.1",
        "port": 62789,
        "protocol": "dokodemo-door",
        "settings": {
          "address": "127.0.0.1"
        },
        "tag": "api"
      }
    ],
    "outbounds": [
      {
        "protocol": "freedom",
        "settings": {}
      },
      {
        "protocol": "blackhole",
        "settings": {},
        "tag": "blocked"
      },
      {
        "tag": "VPS1" ,
        "protocol": "vmess",
        "settings": {
          "vnext": [{
            "address": "1.1.1.1", #落地IP/域名
            "port": 10086, #端口
            "users": [{
              "id": "b6366334-6f42-491b-ae75-8d4bd9c89bde",#UserID
              "security": "auto",
              "alterId": 0
            }]
          }]
        }
      }
    ],
    "policy": {
      "system": {
        "statsInboundDownlink": true,
        "statsInboundUplink": true
      }
    },
    "routing": {
      "rules": [
        {
          "inboundTag": [
            "api"
          ],
          "outboundTag": "api",
          "type": "field"
        },
        {
          "ip": [
            "geoip:private"
          ],
          "outboundTag": "blocked",
          "type": "field"
        },
        {
          "outboundTag": "blocked",
          "protocol": [
            "bittorrent"
          ],
          "type": "field"
        },
        {
            "type": "field",
            "outboundTag": "VPS1",
            "domain": [
                "geosite:netflix",
                "nflxvideo.net",
                "nflxext.com",
                "nflxso.net",
                "geosite:disney",
                "disney.asia",
                "disney.be",
                "disney.bg",
                "disney.ca",
                "disney.ch",
                "disney.co.il",
                "disney.co.jp",
                "disney.co.kr",
                "disney.co.th",
                "disney.co.uk",
                "disney.co.za",
                "disney.com",
                "disney.com.au",
                "disney.com.br",
                "disney.com.hk",
                "disney.com.tw",
                "disney.cz",
                "disney.de",
                "disney.dk",
                "disney.es",
                "disney.fi",
                "disney.fr",
                "disney.gr",
                "disney.hu",
                "disney.id",
                "disney.in",
                "disney.io",
                "disney.it",
                "disney.my",
                "disney.nl",
                "disney.no",
                "disney.ph",
                "disney.pl",
                "disney.pt",
                "disney.ro",
                "disney.ru",
                "disney.se",
                "disney.sg",
                "20thcenturystudios.com.au",
                "20thcenturystudios.com.br",
                "20thcenturystudios.jp",
                "adventuresbydisney.com",
                "babble.com",
                "babyzone.com",
                "bamgrid.com",
                "beautyandthebeastmusical.co.uk",
                "dilcdn.com",
                "disney-asia.com",
                "disney-discount.com",
                "disney-plus.net",
                "disney-portal.my.onetrust.com",
                "disney-studio.com",
                "disney-studio.net",
                "disney.my.sentry.io",
                "disneyadsales.com",
                "disneyarena.com",
                "disneyaulani.com",
                "disneybaby.com",
                "disneycareers.com",
                "disneychannelonstage.com",
                "disneychannelroadtrip.com",
                "disneycruisebrasil.com",
                "disneyenconcert.com",
                "disneyiejobs.com",
                "disneyinflight.com",
                "disneyinternational.com",
                "disneyinternationalhd.com",
                "disneyjunior.com",
                "disneyjuniortreataday.com",
                "disneylatino.com",
                "disneymagicmoments.co.il",
                "disneymagicmoments.co.uk",
                "disneymagicmoments.co.za",
                "disneymagicmoments.de",
                "disneymagicmoments.es",
                "disneymagicmoments.fr",
                "disneymagicmoments.gen.tr",
                "disneymagicmoments.gr",
                "disneymagicmoments.it",
                "disneymagicmoments.pl",
                "disneymagicmomentsme.com",
                "disneyme.com",
                "disneymeetingsandevents.com",
                "disneymovieinsiders.com",
                "disneymusicpromotion.com",
                "disneynewseries.com",
                "disneynow.com",
                "disneypeoplesurveys.com",
                "disneyplus.bn5x.net",
                "disneyplus.com",
                "disneyplus.com.ssl.sc.omtrdc.net",
                "disneyredirects.com",
                "disneysrivieraresort.com",
                "disneystore.com",
                "disneystreaming.com",
                "disneysubscription.com",
                "disneytickets.co.uk",
                "disneyturkiye.com.tr",
                "disneytvajobs.com",
                "disneyworld-go.com",
                "dssott.com",
                "go-disneyworldgo.com",
                "go.com",
                "mickey.tv",
                "moviesanywhere.com",
                "nomadlandmovie.ch",
                "playmation.com",
                "shopdisney.com",
                "shops-disney.com",
                "sorcerersarena.com",
                "spaindisney.com",
                "star-brasil.com",
                "star-latam.com",
                "starwars.com",
                "starwarsgalacticstarcruiser.com",
                "starwarskids.com",
                "streamingdisney.net",
                "thestationbymaker.com",
                "thisispolaris.com",
                "watchdisneyfe.com"
                
            ]
        } 
      ]
    },
    "stats": {}
  }
```