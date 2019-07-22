---
title: git常用命令实践
date: 2019-07-19 11:40:09
tags:
- git
categories:
- git
---

## 关系图
![](/images/git/relation.png)
* Workspace：工作区
* Index/Stage：暂存区
* Repository：本地仓库
* Remote：远程仓库

## 本地仓库

### 新建仓库
```bash
# 在当前目录新建一个Git仓库
$ git init

# 新建一个目录，将其初始化成Git仓库
$ git init [project-name]
```

### 配置信息
```bash
# 显示配置列表
$ git config --list

# 设置用户信息
# 当前仓库设置用户名
$ git config --global user.name "Zhaoxinjie"
# 当前仓库设置用户邮箱
$ git config --global user.email "543008186@qq.com"
# 全局设置用户名
$ git config --global user.name "Zhaoxinjie"
# 全局设置用户邮箱
$ git config --global user.email "543008186@qq.com"
# 不设置值即为查看
```
![](/images/git/1.png)

### 显示工作区和暂存区的状态
```bash
$ git status
```
![](/images/git/2.png)

### 暂存区
```bash
# 添加指定文件到暂存区
$ git add [file1] [file2] ...
# 添加指定目录到暂存区
$ git add [div]
# 添加当前目录所有文件到暂存区
$ git add .
# 删除工作区文件，并将这次删除写入暂存区
$ git rm [file1] [file2] ...
# 停止追踪指定文件，但该文件会保留在工作区
$ git rm --cached [file]
# 改名文件，并将这次改名写入暂存区
$ git mv [file-original] [file-rename]
```