---
title: docker部署vsftpd
date: 2020-12-16 16:09:45
tags: 
- docker
- vsftpd
categories: 
- docker
---

### 官方文档链接
<https://hub.docker.com/r/fauria/vsftpd>

### docker官方镜像(镜像名:版本号，默认latest)
```bash
$ docker pull fauria/vsftpd
```


### 查看镜像列表
```bash
$ docker images
```


### 启动容器
```bash
$ docker  run -idt -v /var/ftp:/home/vsftpd \ 
  -p 20:20 -p 21:21 -p  21100-21110:21100-21110 \ 
  -e FTP_USER=test -e FTP_PASS=test \ 
  -e PASV_ADDRESS=192.168.3.37 \ 
  -e PASV_MIN_PORT=21100 -e PASV_MAX_PORT=21110 \ 
  --name vsftpd --restart=always fauria/vsftpd
```
-v：把容器内/home/vsftpd挂载到主机/var/ftp目录。这里放到var这个公共目录下，其他部分目录可能会导致客户端连不上（需要设置用户权限）
-e FTP_USER=test：设置用户名test
-e FTP_PASS=test：设置密码test
-e PASV_ADDRESS=192.168.3.37：开启ftp被动模式，值为主机ip
-e PASV_MIN_PORT：客户端下载随机端口号下限，与前面docker端口映射一致
-e PASV_MAX_PORT：客户端下载随机端口号上限，与前面docker端口映射一致
--restart=always：docker重启时重启容器

### 测试
![](/images/ftp/ftp1.png)

