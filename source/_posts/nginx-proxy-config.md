---
title: nginx反向代理、静态资源、负载均衡配置
date: 2019-06-21 11:45:12
tags:
- nginx
- proxy
categories:
- nginx
---

### nginx配置文件结构
![](/images/nginx.png)

#### 全局块（main）
该部分配置主要影响Nginx全局，通常包括下面几个部分：
* 配置运行Nginx服务器用户（组）
* worker process数
* Nginx进程PID存放路径
* 错误日志的存放路径
* 配置文件的引入

#### events块
该部分配置主要影响Nginx服务器与用户的网络连接，主要包括：
* 设置网络连接的序列化
* 是否允许同时接收多个网络连接
* 事件驱动模型的选择
* 最大连接数的配置

#### http块
* 定义MIMI-Type
* 自定义服务日志
* 允许sendfile方式传输文件
* 连接超时时间
* 单连接请求数上限

#### server块
* 配置网络监听
* 基于名称的虚拟主机配置
* 基于IP的虚拟主机配置

#### location块
* location配置
* 请求根目录配置
* 更改location的URI
* 网站默认首页配置

### 简单的配置（基本都是默认配置）
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
* user nginx：运行Nginx服务器用户/组 user nobody nobody则默认所有用户都可以启动Nginx进程
* worker_processes：进程数，Nginx服务器实现并发处理服务的关键
* error_log：错误日志存放路径
* pid：Nginx进程是作为系统守护进程在运行，需要在某文件中保存当前运行程序的主进程号，Nginx支持该保存文件路径的自定义

* worker_connections：每一个worker process可以同时开启的最大连接数

* include  default_type：MIME-Type指的是网络资源的媒体类型，即前端请求的资源类型，include指令将mime.types文件包含进来
* log_format：自定义一个名为main的日志格式
* access_log：自定义服务日志 路径+格式（可选）
* sendfile：开启高效文件传输模式（zero copy 方式），避免内核缓冲区数据和用户缓冲区数据之间的拷贝。
* keepalive_timeout：timeout 表示server端对连接的保持时间
* gzip：打开gzip压缩
* include /etc/nginx/conf.d/*.conf：将其他配置文件包含进来

### 主要配置（放到上面/etc/nginx/conf.d/目录下）
```conf
upstream myweb {
    server 192.168.3.125:8081 weight=3;
    server 192.168.3.124:8081 weight=4;
}

server {
    listen       80;
    server_name  192.168.3.123;

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
* upstream：设置负载均衡服务器，后端服务器地址及权重
* server：
   * listen：服务端口
   * server_name：ip/域名，多个用逗号分开
   * location：地址匹配设置，支持正则匹配，也支持条件匹配。从上到下优先匹配。
      * alias：别名。将/zxj/映射目录/opt/resource/myweb/
      * add_header：允许跨域
      * proxy_pass：反向代理

上面配置效果：
   -http://192.168.3.123:80/zxj/xxx  => 192.168.3.123该服务器下 /opt/resource/myweb/目录下xxx
   -http://192.168.3.123:80/ccc/xxx  => http://192.168.3.124:8081/ccc/xxx 或 http://192.168.3.125:8081/ccc/xxx （比例3:4，ccc处不能为zxj，会匹配到上一个）


alias那里也可以用root，试了一些配置路径都没映射成功，马克一下。
小服务器，暂时不用考虑性能优化了。有时间再去研究研究~







