---
title: 用Nginx反代实现docker安装WordPress与其他服务并存
author: 云五
type: post
date: 2021-02-08T02:42:45+00:00
description: |
  目标：一台服务器，一个IP，使用docker安装多个服务（包括WordPress）共存，使用不同的域名或子域名。多服务共存本身不是难点，障碍是WordPress，即如何让WordPress不占用80（和443）端口。

  面向对象要求：懂Unix基础命令，已经购买域名。请确保你的域名DNS设置里，已经将域名指向这台服务器的IP。
  
  我会把我参考的教程列一下，方便不需要这么折腾的人直接用简单的方法装。
url: /tech/shared-service-same-server-wordpress/
categories:
  - 技术
tags:
  - docker
  - nginx
  - reverse proxy
  - VPS
  - WordPress

---

## 前情概要

年初趁促销打折买了一个4 vCPU 8GB RAM 的VPS，自己试着装了一些小服务自己用，比如Tiny RSS、Snibox、Plume之类的。当时我的博客还在DigitalOcean，用它市场里提供的WordPress一键安装，就考虑说把WordPress博客也迁移到这台VPS上，省一点钱……毕竟开不了源就只能先节流了。

迁移时出了一点其他问题，一怒之下重置了VPS，然后用空机器走docker-compose装WordPress，结果对着网上的教程装，直接把80和443端口占用了；即使我改掉docker-compose.yml里的端口再走nginx反代也不行。如果让WordPress占用80和443的话，nginx里再设置其他服务的反代就出问题。

各种搜索，终于在stackoverflow上看到有人问类似的问题，发现问题不在于nginx，而是WordPress的一点奇葩设置（容后再秉），于是……又重置了VPS，一步一步重头搞，设置好了同一台服务器同一个IP多个服务包括WordPress都走nginx反代。

讲完还是觉得有点绕。

我会把我参考的教程列一下，方便不需要这么折腾的人直接用简单的方法装。

目标：一台服务器，一个IP，使用docker安装多个服务（包括WordPress）共存，使用不同的域名或子域名。
面向对象要求：懂Unix基础命令，已经购买域名。

## step 0：勿装Nginx或其他会占用80端口的服务

我的VPS是Ubuntu 20.04的系统，参考的教程是Ubuntu 18.04的，安装过程没什么差别。因为折腾了几次，重新安装了系统，没有安装其他任何服务。

请确保你的域名DNS设置里，已经将域名指向这台服务器的IP。

## step 1：安装docker和docker-compose
我直接上root装的，不是好习惯但方便，不用root的请自行在命令前加上sudo。
```bash
apt update
apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"
apt update
apt install docker-compose
```
安装后可用如下命令检查一下是否安装好了。
```bash
docker -v
docker-compose --version
```
有显示版本号的话就是装好了。

## step 2：用docker安装WordPress，占用80端口

这一步要先使用80端口，安装设置好后再释放，很多人用docker装WordPress想换端口就是在这一步卡住了，第一次启动直接用其他端口是不行的。

参考https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose 这个教程的，在这个教程的基础上我去掉了在container内获取SSL Certificate的部分（SSL放在后面再设置），另外把nginx和wordpress的image都改成了较新的版本。

为WordPress的配置文件新建一个文件夹，我是放在/home/wordpress里的，可以自行设置。
```bash
mkdir wordpress && cd wordpress
mkdir nginx-conf
vi nginx-conf/nginx.conf
```
以下是nginx-conf/nginx.conf的内容，请将example.com替换成你自己的域名。
```conf
server {
        listen 80;
        listen [::]:80;

        server_name example.com www.example.com;

        index index.php index.html index.htm;

        root /var/www/html;

        location ~ /.well-known/acme-challenge {
                allow all;
                root /var/www/html;
        }

        location / {
                try_files $uri $uri/ /index.php$is_args$args;
        }

        location ~ \.php$ {
                try_files $uri =404;
                fastcgi_split_path_info ^(.+\.php)(/.+)$;
                fastcgi_pass wordpress:9000;
                fastcgi_index index.php;
                include fastcgi_params;
                fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                fastcgi_param PATH_INFO $fastcgi_path_info;
        }

        location ~ /\.ht {
                deny all;
        }

        location = /favicon.ico {
                log_not_found off; access_log off;
        }
        location = /robots.txt {
                log_not_found off; access_log off; allow all;
        }
        location ~* \.(css|gif|ico|jpeg|jpg|js|png)$ {
                expires max;
                log_not_found off;
        }
}
```
在/home/wordpress（或你自己定义的其他路径）下新建.env文件存储docker-compose里用到的环境变量。
```bash
vi .env
```
以下是.env文件的内容
```bash
MYSQL_ROOT_PASSWORD=your_mysql_root_password
MYSQL_USER=your_wordpress_database_user
MYSQL_PASSWORD=your_wordpress_database_password
HTTP_PORT=80
```
新建.dockerignore文件，避免将敏感文件传到container内。
```bash
vi .dockerignore
```

以下是.dockerignore文件内容
```bash
.env
docker-compose.yml
.dockerignore
```
新建docker-compose.yml文件
```bash
vi docker-compose.yml
```

以下是docker-compose.yml内容
```bash
version: '3'

services:
  db:
    image: mysql:8.0
    container_name: db
    restart: unless-stopped
    env_file: .env
    environment:
      - MYSQL_DATABASE=wordpress
    volumes:
      - dbdata:/var/lib/mysql
    command: '--default-authentication-plugin=mysql_native_password'
    networks:
      - app-network

  wordpress:
    depends_on:
      - db
    image: wordpress:5.6.1-fpm-alpine
    container_name: wordpress
    restart: unless-stopped
    env_file: .env
    environment:
      - WORDPRESS_DB_HOST=db:3306
      - WORDPRESS_DB_USER=$MYSQL_USER
      - WORDPRESS_DB_PASSWORD=$MYSQL_PASSWORD
      - WORDPRESS_DB_NAME=wordpress
    volumes:
      - wordpress:/var/www/html
    networks:
      - app-network

  webserver:
    depends_on:
      - wordpress
    image: nginx:1.18.0-alpine
    container_name: webserver
    restart: unless-stopped
	env_file: .env
    ports:
      - "$HTTP_PORT:80"
    volumes:
      - wordpress:/var/www/html
      - ./nginx-conf:/etc/nginx/conf.d
    networks:
      - app-network

volumes:
  wordpress:
  dbdata:

networks:
  app-network:
    driver: bridge
```

### 启动WordPress服务
```bash
docker-compose up -d
```

## step 3：访问WordPress进行博客设置

**这是最关键的一步。**

大部分服务直接在配置里改端口就行得通，但是在docker安装WordPress的过程中，如果不先用80端口启动，访问WordPress博客主页配置好的话，改成其他端口是无效的。

访问你的博客域名（本例子里是example.com），会打开WordPress第一次安装配置的界面，选择语言、设定用户、密码，进入WordPress博客管理后台。

确保你重新访问首页打开的是博客首页。

## Step 4：改端口

用docker-compose关闭WordPress服务，更改docker-compose.yml里的设置，把占用的80端口改成其他（比如8050）
```bash
docker-compose down
vi.env
```

修改.env的最后一行为
```bash
HTTP_PORT=8050
```

我用的是8050，当然也可以用其他的，避免使用常规端口即可。

### 重新启动WordPress服务
```bash
docker-compose up -d
```
此时你再访问你的博客，就打不开了，因为我们已经不再使用默认80端口，你可以试着在你的主机里用curl访问
```bash
curl -v http://localhost:8050
```
Response里应该就是你的博客首页。

## Step 5：安装Nginx
安装nginx，增加一个给WordPress的8050端口的反代
```bash
apt install nginx
cd /etc/nginx/sites-enabled/
rm -rf default
cd /etc/nginx/sites-available/
vi wordpress.conf
```

wordpress.conf的内容如下
```conf
server {
    listen      80;
    listen [::]:80;
    server_name example.com;

    location / {
        proxy_pass http://127.0.0.1:8050;
    }
}
```
这只是最最最简单的一个反代配置，实践中根据需要可以增加其他功能/内容。
编辑好后，为这个配置文件增加一个链接
```bash
cd /etc/nginx/sites-enabled/
ln -s ../sites-available/wordpress.conf wordpress.conf
systemctl reload nginx
```
reload nginx服务后，再访问你的博客首页，应该就能打开正常的博客首页了。

**无所谓SSL的到这里就可以了，想增加https访问的请继续。**

## Step 6：安装certbot，获取Let’s Encrypt的免费SSL证书
```bash
apt install certbot python3-certbot-nginx
certbot --nginx -d example.com -d www.example.com
```
申请免费SSL证书时有一些问题，注意同意条款后问是否暴露邮箱时选No。
可能会有一些warning，是因为我们的nginx里的conf文件极简，打开看会有一些变化，凑合着也能用，但建议改成格式整齐一点的：
```bash
cd /etc/nginx/sites-available/
vi wordpress.conf
```
wordpress.conf的内容如下
```bash
server {
    listen      80;
    listen [::]:80;
    server_name example.com;

    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

    location / {
        proxy_pass http://127.0.0.1:8050;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
再reload一下nginx，就可以用https://example.com 来访问自己的博客了。
```bash
systemctl reload nginx
```

## Step 7：安装其他服务并设置反代

其他的小服务什么的比较简单了，用一个不同的端口，在sites-available文件夹下加一个反代再设一下链接即可。

WordPress比较奇葩的是要先占用80端口，安装设置生效后，再改端口生效。

我另外装了Snibox，也是用docker-compose，启动前直接在docker-compose.yml里把端口改成自己想用的即可。

## 参考教程

https://www.digitalocean.com/community/tutorials/how-to-install-wordpress-with-docker-compose
https://stackoverflow.com/questions/48825586/docker-i-cant-map-ports-other-than-80-to-my-wordpress-container
