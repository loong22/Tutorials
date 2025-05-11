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