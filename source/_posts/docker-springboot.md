---
title: docker部署springboot
date: 2019-06-20 16:58:02
tags:
- docker
- springboot
categories:
- docker
---

### 将springboot项目打包成jar包
略

### Dockerfile文件
```Dockerfile
FROM java:8
VOLUME /tmp
COPY myweb-0.0.1-SNAPSHOT.jar /opt/web/app.jar
COPY /config/application.yml /opt/web/config/application.yml
WORKDIR /opt/web
RUN bash -c "touch /app.jar"
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```
FROM：拉取java8为基础对象
VOLUME：指定临时文件目录为/tmp。其效果是在主机 /var/lib/docker 目录下创建了一个临时文件，并链接到容器的/tmp。改步骤是可选的，如果涉及到文件系统的应用就很有必要了。/tmp目录用来持久化到Docker 数据文件夹，因为 Spring Boot 使用的内嵌 Tomcat 容器默认使用/tmp作为工作目录（感觉没什么用啊，在主机该目录下是空的。）
COPY：目录下springboot的jar包及配置文件复制到docker镜像内
WORKDIR：切换工作目录
RUN：执行命令（touch /app.jar）
EXPOSE：暴露8080端口
ENTRYPOINT：执行命令（java -jar app.jar）

### 生成镜像
#### 将jar包、Dockerfile放到同一目录下（如：/opt/web），yml文件放到下级目录config（与Dockerfile中对应，如：/opt/web/config）

#### 生成镜像
```bash
$ docker build -t myweb:v1 .
```
-t：代表要构建的镜像的tag，上文的镜像名为myweb，版本为v1

### 启动容器
```bash
$ docker run -itd --name=myweb -p 8080:8080 -v /opt/web/config/application.yml:/opt/web/config/application.yml -v /opt/web/logs/:/opt/web/logs --privileged=true myweb:v1
```
挂载配置文件和日志文件。
其实在生成镜像的时候，就已经把目录下的配置文件放进去了。不过还是需要挂载出来，不然需要改配置的时候太麻烦了，这个基础镜像里没有yum和vim。。。