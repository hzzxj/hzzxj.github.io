---
title: redis主从-哨兵模式
date: 2022-03-15 09:24:52
tags: 
- docker
- redis
categories: 
- redis
---

## 主从模式

### 1.主从模式的特点
  1、master可以进行读写操作，当读写操作导致数据变化时会自动将数据同步给从数据库
  2、salve只读
  3、master一对多salve


### 2.redis.conf配置

#### master配置 
```
  bind 0.0.0.0        #任意ip都可以连接
  protected-mode yes  #保护模式，需配置bind ip或者设置访问密码；no为关闭，外部网络可以直接访问
  port 6379			      #端口号
  daemonize no        #后台运行，不设置docker会立即退出
  pidfile /var/run/redis_6379.pid	#进程守护文件，就是存放该进程号相关信息的地方
  requirepass password #密码

  ##### SNAPSHOTTING  默认开启RDB
  save 900 1
  save 300 10
  save 60 10000

  dbfilename dump.rdb #rdb文件
  dir ./              #数据存放目录
  rdbcompression yes  #默认开启数据压缩

  ##### 配置AOF
  appendonly yes                                              #开启AOF
  appendfilename "appendonly.aof"                             #本地数据库文件名
  appendfsync everysec                                        #更新日志条件 always 总是，每次数据发生变化会立刻写入，性能较差安全性高；everysec 每秒异步写入，默认值； no 不同步
  auto-aof-rewrite-percentage 100                             #自动化重写百分比，100即一倍
  auto-aof-rewrite-min-size 3gb                               #当AOF文件大小是上次rewrite后大小的一倍且文件大于3GB时触发

  masterauth password                                         #从节点密码；后续使用哨兵，master节点挂了，哨兵选举其中一个slave节点升级为master1，等原master服务重新起来后，变更为从节点，需要连接master1，所以这里需要设置从节点的密码。且要保证主、从的密码一致。

```

#### slave配置
  前面的设置和master一样；
  port端口后面启动的时候指定
  密码要和master的密码一致（后面做哨兵的时候需要）

```
  ##### slave特殊配置
  replicaof 10.0.16.4 6379   #主节点信息
  replica-read-only yes      #默认只读
  masterauth password        #主节点密码

```
### 3.redis启动命令
这里分三个节点，node1为master，node2、node3为slave

##### node1
```
docker run -idt --privileged=true --net=host -v /hzzxj/redis/redis-ms/node1/redis.conf:/usr/local/etc/redis/redis.conf -v /hzzxj/redis/redis-ms/node1/data:/data --name=redis-node1 redis:5.0.14 redis-server /usr/local/etc/redis/redis.conf --port 6379 
```
--net=host  #使用主机端口；使用-p 进行端口映射时，测试发现master节点里slave的信息是127.0.0.1 6379，导致后面做哨兵时，哨兵无法获取争取的salve信息
--port 6379 #设置启动端口为6379

##### node2
```
docker run -idt --privileged=true --net=host -v /hzzxj/redis/redis-ms/node2/redis.conf:/usr/local/etc/redis/redis.conf -v /hzzxj/redis/redis-ms/node2/data:/data --name=redis-node2 redis:5.0.14 redis-server /usr/local/etc/redis/redis.conf --port 6380
```
--port 6380 #设置启动端口为6380；因为在一台服务器上测试，6379已被master占用

##### node3
```
docker run -idt --privileged=true --net=host -v /hzzxj/redis/redis-ms/node3/redis.conf:/usr/local/etc/redis/redis.conf -v /hzzxj/redis/redis-ms/node3/data:/data --name=redis-node3 redis:5.0.14 redis-server /usr/local/etc/redis/redis.conf --port 6381
```
--port 6381 #设置启动端口为6381


### 4.redis主从测试
```
docker ps -a | grep redis- #查看容器状态
```
![](/images/redis/redis-master1.png)


master节点操作
```
docker exec -it redis-node1 redis-cli -p 6379  #进入容器，redis-cli连接redis；-p 指定端口

127.0.0.1:6379> auth password           #用户鉴权

127.0.0.1:6379> info                    #查看节点信息

127.0.0.1:6379> set redis test          #测试写入

127.0.0.1:6379> get redis               #查询
```
![](/images/redis/redis-master2.png)
![](/images/redis/redis-master3.png)


salve节点操作
![](/images/redis/redis-master4.png)

可以看到slave能查询到刚刚master节点写入的信息。而且是只读状态。


## 哨兵模式

### 1.哨兵功能

1、监控redis集群是否正常
2、master出现故障，自动将slave转化为master
3、多哨兵配置的时候，哨兵之间也会自动监控

### 2.哨兵配置
  这里设置三个哨兵，端口分别为26379，26380，26381，其他配置一样
```
bind 0.0.0.0
port 26379                                            # 由于布置在同一台主机上，所以只能区分端口，分别为26379，26380，26381
daemonize no
pidfile /var/run/keydb-sentinel.pid
logfile ""
dir /tmp
protected-mode yes
sentinel monitor mymaster 10.0.16.4 6379 2            # mymaster 集群名称；10.0.16.4 maseter节点ip；6379 master端口；2 需要由2个哨兵选举
sentinel auth-pass mymaster password                  # 这一行要写在sentinel monitor后面，否则会报错
sentinel down-after-milliseconds mymaster 5000
sentinel parallel-syncs mymaster 1
sentinel failover-timeout mymaster 180000
sentinel deny-scripts-reconfig yes
```

### 3.哨兵启动命令
 启动哨兵，用的也是redis的镜像
```
# 哨兵1，端口26379
docker run -idt --name=sentinel-26379 --net=host -v /hzzxj/redis/sentinel/26379/sentinel-26379.conf:/usr/local/etc/redis/sentinel.conf redis:5.0.14 redis-sentinel /usr/local/etc/redis/sentinel.conf

# 哨兵1，端口26379
docker run -idt --name=sentinel-26380 --net=host -v /hzzxj/redis/sentinel/26380/sentinel-26380.conf:/usr/local/etc/redis/sentinel.conf redis:5.0.14 redis-sentinel /usr/local/etc/redis/sentinel.conf

# 哨兵1，端口26379
docker run -idt --name=sentinel-26381 --net=host -v /hzzxj/redis/sentinel/26381/sentinel-26381.conf:/usr/local/etc/redis/sentinel.conf redis:5.0.14 redis-sentinel /usr/local/etc/redis/sentinel.conf
```

### 4.哨兵测试
  查看容器状态
![](/images/redis/redis-sentinel1.png)

  启动后哨兵配置文件会自动写入集群信息
![](/images/redis/redis-sentinel2.png)

  哨兵启动日志
![](/images/redis/redis-sentinel3.png)

  这时候把master节点停了；docker stop redis-node1；查看哨兵日志发现master迁移到6380端口的节点上
![](/images/redis/redis-sentinel4.png)

  尝试往6380写入数据，可以写入了。再查看节点信息，成为了master节点，slave剩下6381
![](/images/redis/redis-sentinel5.png)
![](/images/redis/redis-sentinel6.png)

  重新拉起node1节点，并不会把node1重新做为master，而是作为slave重新加入集群
![](/images/redis/redis-sentinel7.png)