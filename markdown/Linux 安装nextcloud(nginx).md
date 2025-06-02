
参考教程地址
------

[https://blog.csdn.net/u012291718/article/details/113805525](https://blog.csdn.net/u012291718/article/details/113805525)

1\. 安装&配置mysql
--------------

```
sudo apt update

#安装mysql
sudo apt install mysql-server

#配置mysql
sudo mysql

#创建数据库和用户并配置权限
create database nextcloud;

create user nextclouduser@localhost identified by 'your-password';

grant all privileges on nextcloud.* to nextclouduser@localhost identified by ‘your-password’;
#上面这句话可能会报错，因为mysql版本太高了，可以执行下面这句话
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextclouduser'@'localhost';


flush privileges;

exit;
```

2\. 安装&配置nginx和php-8.0
----------------------

2.1 安装nginx和php-8.0
-------------------

```
sudo apt update

sudo apt install nginx

sudo apt install imagemagick php-imagick

sudo apt-get install php8.2-fpm php8.2-cli php8.2-common php8.2-mysql php8.2-xml php8.2-xmlrpc php8.2-curl php8.2-gd php8.2-imagick php8.2-cli php8.2-dev php8.2-imap php8.2-mbstring php8.2-opcache php8.2-soap php8.2-zip php8.2-intl

sudo apt install php-pear php8.0-dev
```

2.2 配置nginx文件
-------------

```
vim /etc/nginx/sites-available/example.com

ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/example.com


#nginx配置示例文件如下,复制粘贴到文件/etc/nginx/sites-available/example.com内

upstream php-handler {
    server 127.0.0.1:9000;
    #server unix:/var/run/php/php7.4-fpm.sock;
}

server {
    listen 80;
    listen [::]:80;
    server_name example.com;

    location /.well-known/acme-challenge/ {
        root /home/wwwroot/example.com;
        try_files $uri =404;
    }
    location / {
        return 301 https://$host$request_uri;
    }
    
    # Enforce HTTPS
    return 301 https://$server_name$request_uri;
    
    #error_log /var/log/nginx/example.com.error.log;
    #access_log /var/log/nginx/example.com.access.log;
}

server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name example.com;
    ssl_certificate /root/.acme.sh/example.com_ecc/fullchain.cer;
    ssl_certificate_key /root/.acme.sh/example.com_ecc/example.com.key;

    #php配置8.0
    location ~ \.php(?:$|/) {
        include fastcgi_params;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/run/php/php8.0-fpm.sock; # 检查 PHP-FPM 版本和路径
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param HTTPS on;
        fastcgi_param modHeadersAvailable true; # 预防XSS
        fastcgi_param front_controller_active true;
        fastcgi_intercept_errors on;
        fastcgi_request_buffering off;
    }
    
#php配置7.4	
#	# Ensure this block, which passes PHP files to the PHP process, is above the blocks
#	# which handle static assets (as seen below). If this block is not declared first,
#	# then Nginx will encounter an infinite rewriting loop when it prepends
#	# `/nextcloud/index.php` to the URI, resulting in a HTTP 500 error response.
#	location ~ \.php(?:$|/) {
#		include fastcgi_params;
#		fastcgi_split_path_info ^(.+\.php)(/.*)$;
#		try_files $fastcgi_script_name =404;
#		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
#		fastcgi_param PATH_INFO $fastcgi_path_info;
#		#Avoid sending the security headers twice
#		fastcgi_param modHeadersAvailable true;
#		fastcgi_param front_controller_active true;
#		fastcgi_pass unix:/run/php/php7.4-fpm.sock;
#		fastcgi_intercept_errors on;
#		fastcgi_request_buffering off;
#	}
    
    
    # HSTS settings
    # WARNING: Only add the preload option once you read about
    # the consequences in https://hstspreload.org/. This option
    # will add the domain to a hardcoded list that is shipped
    # in all major browsers and getting removed from this list
    # could take several months.
    #add_header Strict-Transport-Security "max-age=15768000; includeSubDomains; preload;" always;

    # set max upload size and increase upload timeout:
    client_max_body_size 512M;
    client_body_timeout 300s;
    fastcgi_buffers 64 4K;

    # Enable gzip but do not remove ETag headers
    gzip on;
    gzip_vary on;
    gzip_comp_level 4;
    gzip_min_length 256;
    gzip_proxied expired no-cache no-store private no_last_modified no_etag auth;
    gzip_types application/atom+xml application/javascript application/json application/ld+json application/manifest+json application/rss+xml application/vnd.geo+json application/vnd.ms-fontobject application/wasm application/x-font-ttf application/x-web-app-manifest+json application/xhtml+xml application/xml font/opentype image/bmp image/svg+xml image/x-icon text/cache-manifest text/css text/plain text/vcard text/vnd.rim.location.xloc text/vtt text/x-component text/x-cross-domain-policy;

    # Pagespeed is not supported by Nextcloud, so if your server is built
    # with the `ngx_pagespeed` module, uncomment this line to disable it.
    #pagespeed off;

    # HTTP response headers borrowed from Nextcloud `.htaccess`
    add_header Referrer-Policy                      "no-referrer"   always;
    add_header X-Content-Type-Options               "nosniff"       always;
    add_header X-Download-Options                   "noopen"        always;
    add_header X-Frame-Options                      "SAMEORIGIN"    always;
    add_header X-Permitted-Cross-Domain-Policies    "none"          always;
    add_header X-Robots-Tag                         "none"          always;
    add_header X-XSS-Protection                     "1; mode=block" always;

    # Remove X-Powered-By, which is an information leak
    fastcgi_hide_header X-Powered-By;

    # Path to the root of your installation
    root /var/www/nextcloud;

    # Specify how to handle directories -- specifying `/index.php$request_uri`
    # here as the fallback means that Nginx always exhibits the desired behaviour
    # when a client requests a path that corresponds to a directory that exists
    # on the server. In particular, if that directory contains an index.php file,
    # that file is correctly served; if it doesn't, then the request is passed to
    # the front-end controller. This consistent behaviour means that we don't need
    # to specify custom rules for certain paths (e.g. images and other assets,
    # `/updater`, `/ocm-provider`, `/ocs-provider`), and thus
    # `try_files $uri $uri/ /index.php$request_uri`
    # always provides the desired behaviour.
    index index.php index.html /index.php$request_uri;

    # Rule borrowed from `.htaccess` to handle Microsoft DAV clients
    location = / {
        if ( $http_user_agent ~ ^DavClnt ) {
            return 302 /remote.php/webdav/$is_args$args;
        }
    }

    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    # Make a regex exception for `/.well-known` so that clients can still
    # access it despite the existence of the regex rule
    # `location ~ /(\.|autotest|...)` which would otherwise handle requests
    # for `/.well-known`.
    location ^~ /.well-known {
        # The rules in this block are an adaptation of the rules
        # in `.htaccess` that concern `/.well-known`.

        location = /.well-known/carddav { return 301 /remote.php/dav/; }
        location = /.well-known/caldav  { return 301 /remote.php/dav/; }

        location /.well-known/acme-challenge    { try_files $uri $uri/ =404; }
        location /.well-known/pki-validation    { try_files $uri $uri/ =404; }

        # Let Nextcloud's API for `/.well-known` URIs handle all other
        # requests by passing them to the front-end controller.
        return 301 /index.php$request_uri;
    }

    # Rules borrowed from `.htaccess` to hide certain paths from clients
    location ~ ^/(?:build|tests|config|lib|3rdparty|templates|data)(?:$|/)  { return 404; }
    location ~ ^/(?:\.|autotest|occ|issue|indie|db_|console)                { return 404; }

    # Ensure this block, which passes PHP files to the PHP process, is above the blocks
    # which handle static assets (as seen below). If this block is not declared first,
    # then Nginx will encounter an infinite rewriting loop when it prepends `/index.php`
    # to the URI, resulting in a HTTP 500 error response.
    location ~ \.php(?:$|/) {
        # Required for legacy support
        rewrite ^/(?!index|remote|public|cron|core\/ajax\/update|status|ocs\/v[12]|updater\/.+|oc[ms]-provider\/.+|.+\/richdocumentscode\/proxy) /index.php$request_uri;

        fastcgi_split_path_info ^(.+?\.php)(/.*)$;
        set $path_info $fastcgi_path_info;

        try_files $fastcgi_script_name =404;

        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $path_info;
        fastcgi_param HTTPS on;

        fastcgi_param modHeadersAvailable true;         # Avoid sending the security headers twice
        fastcgi_param front_controller_active true;     # Enable pretty urls
        fastcgi_pass php-handler;

        fastcgi_intercept_errors on;
        fastcgi_request_buffering off;

        fastcgi_max_temp_file_size 0;
    }

    location ~ \.(?:css|js|svg|gif|png|jpg|ico|wasm|tflite|map)$ {
        try_files $uri /index.php$request_uri;
        expires 6M;         # Cache-Control policy borrowed from `.htaccess`
        access_log off;     # Optional: Don't log access to assets

        location ~ \.wasm$ {
            default_type application/wasm;
        }
    }

    location ~ \.woff2?$ {
        try_files $uri /index.php$request_uri;
        expires 7d;         # Cache-Control policy borrowed from `.htaccess`
        access_log off;     # Optional: Don't log access to assets
    }

    # Rule borrowed from `.htaccess`
    location /remote {
        return 301 /remote.php$request_uri;
    }

    location / {
        try_files $uri $uri/ /index.php$request_uri;
    }
}
```

3 安装nextcloud
-------------

```sh
#创建文件夹(如果没有)
mkdir /var/www/

#进入文件夹
cd /var/www/

#下载nextcloud的zip包
wget https://download.nextcloud.com/server/releases/nextcloud-29.0.2.zip

#解压到当前目录
unzip nextcloud-29.0.2.zip -d .

#设置权限
chown -R www-data:www-data /var/www/nextcloud/

#访问网页进行配置
https://example.com

#修改/var/www/nextcloud/config/config.php文件如下
#如果没有就加上
'overwritehost' => 'cloud.example.com',
'dbport' => '3306',
```