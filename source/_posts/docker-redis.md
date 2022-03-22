---
title: docker部署redis
date: 2019-06-20 09:24:52
tags: 
- docker
- redis
categories: 
- docker
---

### 1.官方文档链接
<https://hub.docker.com/_/redis>

### 2.docker官方镜像(镜像名:版本号，默认latest)
```bash
 docker pull redis:5.0.14
 docker pull registry.docker-cn.com/library/redis:5.0.14   #docker中国镜像加速
```


### 3.简单启动容器
```bash
 docker run -itd -p 6380:6379 –name="redis" docker.io/redis
```
-it：以交互模式运行容器，并为容器分配一个伪终端
-p：端口映射，主机端口:容器端口
--name：为容器指定名称

### 4.挂载外部配置及数据持久化

#### redis.conf 下载
<https://redis.io/topics/config>

#### 修改redis.conf
  bind 0.0.0.0        #任意ip都可以连接
  protected-mode yes	#保护模式，需配置bind ip或者设置访问密码；no为关闭，外部网络可以直接访问
  port 6379			      #端口号
  daemonize no		    #后台运行，不设置docker会立即退出
  pidfile /var/run/redis_6379.pid	#进程守护文件，就是存放该进程号相关信息的地方
  requirepass zhaoxinjie #密码

  ##### SNAPSHOTTING   默认开启RDB
  save 900 1
  save 300 10
  save 60 10000

  dbfilename dump.rdb	#rdb文件
  dir ./				#数据存放目录
  rdbcompression yes	#默认开启数据压缩

  ##### 配置AOF
  appendonly yes	#开启AOF
  appendfilename "appendonly.aof"	#本地数据库文件名
  appendfsync everysec	#更新日志条件 always 总是，每次数据发生变化会立刻写入，性能较差安全性高；everysec 每秒异步写入，默认值； no 不同步
  auto-aof-rewrite-percentage 100	#自动化重写百分比，100即一倍
  auto-aof-rewrite-min-size 3gb	#当AOF文件大小是上次rewrite后大小的一倍且文件大于3GB时触发


#### 启动容器
```bash
docker run -idt --privileged=true -p 6379:6379 -v /hzzxj/redis/redis-ms/node1/redis.conf:/usr/local/etc/redis/redis.conf -v /hzzxj/redis/redis-ms/node1/data:/data --name=redis-node1 redis:5.0.14 redis-server /usr/local/etc/redis/redis.conf
```
redis-server /usr/local/etc/redis/redis.conf    #按照指定配置文件启动
/hzzxj/redis/redis-ms/node1/redis.conf:/usr/local/etc/redis/redis.conf    #挂载配置文件 主机：容器
/hzzxj/redis/redis-ms/node1/data:/data    #挂载数据存放目录  主机：容器

#### 测试
```bash
docker ps -a | grep node1   #查看容器运行状态

docker exec -it redis-node1 redis-cli -h 127.0.0.1 -p 6379  #进入容器使用redis-cli命令   -h -p 可省略

127.0.0.1:6379> AUTH password   #鉴权
```
![](/images/redis/redis1.png)
![](/images/redis/redis2.png)