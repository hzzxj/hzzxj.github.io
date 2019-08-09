---
title: docker部署mysql
date: 2019-08-08 20:00:40
tags: 
- docker
- mysql
categories: 
- docker
---

### 官方文档链接
<https://hub.docker.com/_/mysql>

### docker官方镜像(镜像名:版本号，默认latest)
```bash
# 这里用5.7版本
$ docker pull mysql:5.7
```

### 简单启动mysql容器
```bash
$ docker run -id --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```
-e： 添加环境变量。 MYSQL_ROOT_PASSWORD 这里添加root用户的密码。其他环境变量可以看官方的文档。如果使用已包含数据库的数据目录启动容器，则任何变量都不会产生任何影响。

### 使用自定义配置文件启动
```bash
$ docker run -id --name mysql -p 3306:3306  -v /opt/mysql:/etc/mysql/conf.d  -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```
-v /opt/mysql:/etc/mysql/conf.d：将宿主机的/opt/mysql目录挂载到容器内/etc/mysql/conf.d。自定义的配置文件放到/opt/mysql目录下就行了。

### 传入配置参数启动
不想挂载配置文件，又想修改mysql的配置参数。
```bash
$ docker run -id --name mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7 --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
```
--character-set-server=utf8mb4：设置数据库字符集编码为utf8mb4（utf8的超集并完全兼容utf8）
--collation-server=utf8mb4_unicode_ci：设置排序字符集为utf8mb4_unicode_ci

查看可用选项的完整列表
```bash
$ docker run -it --rm mysql:tag --verbose --help
```

### 挂载数据目录
```
$ docker run -id --name mysql -p 3306:3306  -v /opt/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 mysql:5.7
```
-v /opt/mysql/data:/var/lib/mysql：将宿主机的/opt/mysql/data目录挂载到容器内/var/lib/mysql。 这样mysql的数据文件就保存在宿主机的/opt/mysql/data目录。