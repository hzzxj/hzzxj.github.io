---
title: docker部署postgres
date: 2019-06-19 17:41:25
tags: 
- docker
- postgresql
categories: 
- docker
---

### 官方文档链接
<https://hub.docker.com/_/postgres>

### docker官方镜像(镜像名:版本号，默认latest)
```bash
$ docker pull postgres:11
```

### docker中国镜像加速(官方镜像慢的话可以用这个)
```bash
$ docker pull registry.docker-cn.com/library/postgres
```

### 查看镜像列表
```bash
$ docker images
```

### 简单启动容器
```bash
$ docker run -itd -p 5432:5432 -name="postgres" -e POSTGRES_PASSWORD=zhaoxinjie postgres:11
```
-it：以交互模式运行容器，并为容器分配一个伪终端
-p：端口映射，主机端口:容器端口
--name：为容器指定名称
-e POSTGRES_PASSWORD：设置环境变量。设置postgres用户的密码

### 挂载外部配置及数据
#### 获取默认的配置文件并放到/root/postgres/目录下
```bash
$ docker run -i --rm postgres cat /usr/share/postgresql/postgresql.conf.sample > /root/postgres/postgres.conf 
```
#### 根据自定义配置文件启动及挂载数据到容器外
```bash
$ docker run -itd --name postgres -p 5432:5432 -e POSTGRES_PASSWORD=zhaoxinjie --privileged=true -v /root/postgres/data/:/var/lib/postgresql/data -v /root/postgres/postgres.conf:/etc/postgresql/postgresql.conf postgres:11 -c 'config_file=/etc/postgresql/postgresql.conf' 
```
--privileged=true：容器内的root拥有真正的权限，否则容器内的root只是外部普通用户权限（不设置启动会报错）
-v：挂载文件
上面的配置文件路径（根据实际情况修改）：/root/postgres/postgres.conf
上面的挂载数据路径（根据实际情况修改）：/root/postgres/data/