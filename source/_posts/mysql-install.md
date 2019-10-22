---
title: win10安装mysql
date: 2019-10-22 09:51:26
tags: mysql
categories: mysql
---

### 下载
官网下载地址
<https://cdn.mysql.com//Downloads/MySQL-5.7/mysql-5.7.28-winx64.zip>

### 解压
解压到自定义目录，不建议放在C盘。这里我解压到E:\mysql-5.7.28-winx64

### 创建配置文件
解压后发现目录下没有默认的配置文件，这里手动创建一个my.ini的配置文件
```
[mysqld]
port=3306
basedir=E:\mysql-5.7.28-winx64
datadir=E:\mysql-5.7.28-winx64\data
max_connections=200
character-set-server=utf8
default-storage-engine=INNODB
sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
[mysql]
default-character-set=utf8
```
basedir：解压mysql的根目录
datadir：数据存放目录，这个目录不用手动创建，在安装时会自动创建

### 设置环境变量
新建一个MYSQL_HOME的环境变量，并添加到path里
![](/images/mysql/1.png)
![](/images/mysql/2.png)

### 安装
管理员权限运行cmd
因为设置了环境变量，所以可以在任意目录使用mysqld命令。
```bash
# 这里会初始化上面的data文件夹，时间有点久
mysqld --initialize

# 安装
mysqld -install

# 启动服务
net start mysql
```
![](/images/mysql/3.png)

### 修改初始密码
在data目录下找到XXX.err文件并打开，找到初始化的随机密码password
![](/images/mysql/4.png)

使用密码进入mysql
![](/images/mysql/5.png)

修改密码
![](/images/mysql/6.png)

### 创建用户、分配权限
```bash
# 新建用户
create user 'username'@'host' identified by 'password'; # host="localhost"为本地用户 host="ip"为ip登录  host="%"为外网登录

# 查看用户
select host,user,authentication_string from mysql.user;
```
这里创建了一个用户名为test，密码为123456，允许外网登录的用户
![](/images/mysql/7.png)

```bash
# 分配权限
grant privileges on databasename.tablename to 'username'@'host' identified by 'password';
# priveleges(权限列表),可以是all priveleges, 表示所有权限，也可以是select、update等权限，多个权限的名词,相互之间用逗号分开。
# on用来指定权限针对哪些库和表。
# *.* 中前面的*号用来指定数据库名，后面的*号用来指定表名。
# to 表示将权限赋予某个用户, 如 jack@'localhost' 表示jack用户，@后面接限制的主机，可以是IP、IP段、域名以及%，%表示任何地方。
# identified by指定用户的登录密码,该项可以省略。
# WITH GRANT OPTION 这个选项表示该用户可以将自己拥有的权限授权给别人。注意：经常有人在创建操作用户的时候不指定WITH GRANT OPTION选项导致后来该用户不能使用GRANT命令创建用户或者给其它用户授权。
# 可以使用GRANT重复给用户添加权限，权限叠加，比如你先给用户添加一个select权限，然后又给用户添加一个insert权限，那么该用户就同时拥有了select和insert权限。
```
![](/images/mysql/8.png)