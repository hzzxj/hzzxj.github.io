---
title: docker部署redis
date: 2019-06-20 09:24:52
tags: 
- docker
- redis
categories: 
- docker
---

### 官方文档链接
<https://hub.docker.com/_/redis>

### docker官方镜像(镜像名:版本号，默认latest)
```bash
$ docker pull redis:5.0.4
```

### docker中国镜像加速(官方镜像慢的话可以用这个)
```bash
$ docker pull registry.docker-cn.com/library/redis
```

### 查看镜像列表
```bash
$ docker images
```

### 简单启动容器
```bash
$ docker run -itd -p 6380:6379 –name="redis" docker.io/redis
```
-it：以交互模式运行容器，并为容器分配一个伪终端
-p：端口映射，主机端口:容器端口
--name：为容器指定名称

### 挂载外部配置及数据持久化
#### 修改redis.conf
  daemonize no：注释掉注释掉注释掉（用守护线程启动）
  requirepass：设置密码
  bind：限制ip访问，注释掉
  appendonly yes：数据持久化

#### 启动容器
```bash
$ docker run -itd --privileged=true -p 6379:6379 -v /root/redis/redis.conf:/usr/local/etc/redis/redis.conf -v /root/redis/data:/data  --name redis redis:5.0.4 redis-server /usr/local/etc/redis/redis.conf --appendonly yes
```
redis-server ...：按照指定配置文件启动
--appendonly yes：数据持久化
上面的配置文件路径（根据实际情况修改）：/root/redis/redis.conf
上面的挂载持久化路径（根据实际情况修改）：/root/redis/data
