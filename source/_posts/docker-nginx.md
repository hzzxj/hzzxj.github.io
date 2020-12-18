---
title: docker部署nginx
date: 2019-06-29 09:41:25
tags: 
- docker
- nginx
categories: 
- docker
---

### 官方文档链接
<https://hub.docker.com/_/nginx>

### docker官方镜像(镜像名:版本号，默认latest)
```bash
$ docker pull nginx:1.14
```

### 查看镜像列表
```bash
$ docker images
```

### 简单的反向代理
新编辑/opt/nginx/myweb.conf配置文件
```
upstream myweb {
    server 47.97.195.164:8080;
}

server {
    listen       80;
    server_name  47.97.195.164;

    location / {
      proxy_pass  http://myweb;
      index index.html index.htm;
    }

}
```
初始化容器
```bash
$ docker run -itd --name=nginx -v /opt/nginx/myweb.conf:/etc/nginx/conf.d/myweb.conf -p 80:80 --privileged=true nginx:1.14
```


### 简单的反向代理 + 静态资源服务器
#### 新编辑/opt/nginx/myweb.conf配置文件
```conf
upstream myweb {
    server 47.97.195.164:8080 weight=3;
    server 47.97.195.165:8080 weight=4;;
}

server {
    listen       80;
    server_name  47.97.195.164;

    location /zxj/ {
      alias       /opt/resource/myweb/;
      add_header  Access-Control-Allow-Origin *;
    }

    location / {
      proxy_pass  http://myweb;
      index index.html index.htm;
    }

}
```
#### 修改nginx.conf 这里开启gzip压缩，不用的可以略过这步
获取默认的nginx.cong配置文件
```bash
$ docker run -i --rm nginx:1.14 cat /etc/nginx/nginx.conf > nginx.conf
```
修改如下（gzip）
```conf
user  nginx;
worker_processes  1;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    keepalive_timeout  65;

    #gzip  on;
    gzip on;
    gzip_min_length 1k;
    gzip_buffers 4 16k;
    #gzip_http_version 1.0;
    gzip_comp_level 2;
    gzip_types text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary off;
    gzip_disable "MSIE [1-6]\.";

    include /etc/nginx/conf.d/*.conf;
}
```
#### 初始化容器
```bash
$ docker run -itd --name=nginx -v /opt/nginx/nginx.conf:/etc/nginx/nginx.conf -v /opt/nginx/myweb.conf:/etc/nginx/conf.d/myweb.conf -v /opt/resource/:/opt/resource -p 80:80 --privileged=true nginx:1.14
```
注意目录的对应。
/opt/resource/：静态资源的目录

####
如果访问文件时出现403，有可能是没有该目录的操作权限。
```
$ chmod -R 777 /opt/resource
```
