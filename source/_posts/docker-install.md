---
title: centos7下docker安装
date: 2019-06-19 17:33:29
tags: 
- docker
categories: 
- docker
---

### 检查Linux内核版本，docker要求3.10及以上
```bash
$ uname -r
```

### 安装docker
```bash
$ yum install docker
```

### 启动docker
```bash
$ systemctl start docker
```

### 查看docker版本
```bash
$ docker -v
```

### 停止docker
```bash
$ systemctl stop docker
```

### 开机启动docker
```bash
$ systemctl enable docker
```
