---
title: git常用命令
date: 2019-07-03 16:47:28
tags:
- git
categories:
- git
---

### git 常用命令
git add 文件名：追踪指定文件
git add .：追踪所有文件
git commit -m '注释'：提交到本地仓库
git push：推送远程仓库
git pull：拉取
git status：查看当前提交状态
git branch：查看分支
git branch 分支名：创建分支，不切换
git branch -d：删除分支
git checkout 分支名：切换到某个分支
git checkout -b 分支名：创建分支，并切换到该分支
git merge 分支名：合并分支
git reset HEAD -- file：清空add命令向暂存区提交的关于file文件的修改
git reset --hard HEAD：版本回退
git reflog：查看所有操作日志
git stash：将文件放入暂存区
git stash list：查看暂存区文件
git stash applly 暂存区id：将文件从暂存区取出
git stash pop：将文件从暂存区取出,并删除暂存区的文件
git stash clear：清除暂存区
git stash branch 分支名称：暂存区创建分支
git diff 文件名：比较工作目录和暂存区的不同
git diff --cached 文件名：比较暂存区和远程仓库的不同
git diff commitID 文件名：比较工作目录和远程仓库的不同
git tag -a 标签名称 -m '注释'：创建标签
git tag：查看标签
git push origin 标签名称：推送标签到远程仓库
git push origin --tags：推送所有的标签到远程仓库




