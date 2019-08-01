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

### 添加文件到暂存区
```bash
# 添加指定文件到暂存区
$ git add [file1] [file2] ...

# 添加指定目录到暂存区
$ git add [div]

# 添加当前目录所有文件到暂存区
$ git add .
```
这里可以看到新添加了一个文件
![](/images/git/3.png)

### 提交到本地仓库
```bash
# 提交暂存区到仓库区，message是每次提交的说明，查看历史记录的时候会显示
$ git commit -m [message]

# 提交暂存区指定文件到仓库区
$ git commit [file1] [file2] -m [message]

# 提交工作区自上次commit之后的变化，直接到仓库区
$ git commit -a

# 提交时显示所有diff信息
$ git commit -v

# 使用一次新的commit，替代上一次提交
# 如果代码没有变化，则用来修改上一次commit的提交信息
$ git commit --amend -m [message]
```
可以看到在这次提交中，一个文件变动，增加了1行
![](/images/git/4.png)

新增加一个test1.txt，并修改test.txt。然后用--amend替代上一次提交。
![](/images/git/5.png)

### 查看文件提交纪录
```bash
# 显示当前分支的历史版本
$ git log

# 显示commit历史，单行显示（--pretty=oneline）,以及每次commit发生变更的文件（--stat）
$ git log --stat --pretty=oneline查看某文件暂存区和工作区的差异
```
![](/images/git/6.png)
![](/images/git/7.png)

### 查看文件变动
```bash
# 查看某文件暂存区和工作区的差异
$ git diff [file]

# 查看暂存区和工作区的差异
$ git diff

# 查看某文件暂存区和本地仓库的差异
$ git diff --cached [file]

# 查看暂存区和本地仓库的差异
$ git diff --cached

# 显示工作区与当前分支最新commit之间的差异
$ git diff HEAD
```
这里可以看到test.txt删除了第2行，添加了234567行
![](/images/git/8.png)

把test.txt添加到暂存区，查看修改
![](/images/git/9.png)

### 撤销工作区的修改
```bash
# 恢复暂存区的指定文件到工作区，譬如在工作区修改了test.txt，还没有add，但是现在突然不要这修改了，可以用这个方式撤销
$ git checkout [file]
```
![](/images/git/10.png)

### 撤销暂存区的修改
```bash
# 将add的文件撤销到工作区
$ git reset HEAD 
```
可以看到执行git reset HEAD，之前git add的文件重新回到了工作区
![](/images/git/11.png)

### 版本回退
```bash
# commitId为每次commit的token，可以用git log查看。commitId为空或者为HEAD，为当前版本，即上一次commit的状态
$ git reset --hard [commintId]

# 一个^代表回退一个版本
$ git reset --hard^

# ~后跟的数字代表回退几个版本
$ git reset --hard~1
```
![](/images/git/12.png)


### 查看当前分支的提交纪录
```bash
$ git reflog
```
![](/images/git/13.png)

## 远程仓库

### 从远程仓库克隆
```bash
# 从远程仓库克隆
$ git clone https://gitee.com/zzdzxj/test1.git
```

### 本地仓库与远程仓库连接
```bash
# 如果已经有本地仓库，想要和远程仓库连接
$ git remote add origin https://gitee.com/zzdzxj/test1.git

# 与本地仓库连接之后，需要把本地修改先推送到远程仓库上
$ git push -u origin master
```
gitee上新建一个仓库的例子
![](/images/git/14.png)

### 使用fetch从远程仓库拉取代码/查看更新
```bash
# 从远程仓库拉取代码，不合并。可用来查看有没有其他人更新
$ git fetch origin master

# 比较本地的master分支与origin的master分支的区别
$ git log -p master  ..origin/master

# 进行合并
$ git merge origin/master
```
![](/images/git/15.png)

### 使用pull从远程仓库拉取代码
```bash
# 从远程仓库拉取代码并合并。如果要拉取的分支与当前分支连接，参数可省略
$ git pull [remote] [branch]
```
![](/images/git/16.png)

### 推送代码到远程仓库
```bash
# 推送代码到远程仓库。如果要拉取的分支与当前分支连接，参数可省略
# 第一次推送可能要登录
$ git push [remote] [branch]
```
![](/images/git/17.png)

## 分支管理

### 查看分支
```bash
# 查看本地分支
$ git branch

# 查看远程分支
$ git branch -r

# 查看本地分支和远程分支
$ git branch -a
```
![](/images/git/18.png)

### 新建分支
```bash
# 新建分支
$ git branch [branch-name]

# 新建分支，并切换到该分支
$ git checkout -b [branch-name]
```
git checkout -b [branch-name] 
等价于  
git branch [branch-name]
git checkout [branch-name]
![](/images/git/19.png)

### 从远程仓库拉取分支
```bash
# clone下来的新仓库，只有默认分支的代码，如果需要拉取其他分支
$ git checkout -b [branch-name] [remote]/[branch]
```
以程仓库的develop为模板，在本地建立一个dev的分支，并切换到dev分支。
![](/images/git/20.png)


### 本地分支与远程分支建立追踪关系
```bash
$ git branch --set-upstream-to=origin/[remote branch] [branch]
```
将本地分支dev与远程分支develop建立连接
![](/images/git/21.png)

### 切换分支
```bash
# 切换到指定分支，并更新工作区
$ git checkout [branch]

# 切换到上一个分支
$ git checkout -
```
![](/images/git/22.png)

### 删除分支
```bash
$ git branch -d [branch]
```
![](/images/git/23.png)

### 删除远程分支
```
$ git push origin --delete [remote branch]
```
删除远程仓库上的develop分支
![](/images/git/24.png)

### 合并分支
```bash
# 合并指定分支到当前分支
$ git merge [branch]
```
![](/images/git/25.png)

## 储藏

git stash命令的作用就是将目前还不想提交的但是已经修改的内容进行保存至堆栈中，后续可以在某个分支上恢复出堆栈中的内容。这也就是说，stash中的内容不仅仅可以恢复到原先开发的分支，也可以恢复到其他任意指定的分支上。git stash作用的范围包括工作区和暂存区中的内容，也就是说没有提交的内容都会保存至堆栈中。
### 应用场景
* 当正在dev分支上开发某个项目，这时项目中出现一个bug，需要紧急修复，但是正在开发的内容只是完成一半，还不想提交，这时可以用git stash命令将修改的内容保存至堆栈区，然后顺利切换到hotfix分支进行bug修复，修复完成后，再次切回到dev分支，从堆栈中恢复刚刚保存的内容。 
* 由于疏忽，本应该在dev分支开发的内容，却在master上进行了开发，需要重新切回到dev分支上进行开发，可以用git stash将内容保存至堆栈中，切回到dev分支后，再次恢复内容即可。 

### git stash
能够将所有未提交的修改（工作区和暂存区）保存至堆栈中，用于后续恢复当前工作目录。
```bash
$ git stash
```
![](/images/git/26.png)

### git stash save
等同于git stash，只是多了注释
```bash
$ git stash save [message]
```
![](/images/git/27.png)

### git stash list
查看当前stash中的内容
```bash
$ git stash list
```
可以看到之前保存的2个
![](/images/git/28.png)

### git stash pop
将当前stash中的内容弹出，并应用到当前分支对应的工作目录上。 
注：该命令将堆栈中最近保存的内容删除（栈是先进后出） 
```bash
$ git stash pop
```
![](/images/git/29.png)

### git stash apply
将堆栈中的内容应用到当前目录，不同于git stash pop，该命令不会将内容从堆栈中删除，也就说该命令能够将堆栈的内容多次应用到工作目录中，适应于多个分支的情况。
也可以使用git stash apply + stash名字（如stash@{1}）指定恢复哪个stash到当前的工作目录。
```bash
$ git stash apply
```
![](/images/git/30.png)

### git stash drop
从堆栈中移除某个指定的stash，不指定删除stash@{0}
```bash
$ git stash drop [stash-name]
```
![](/images/git/31.png)

### git stash clean
清除堆栈中的所有内容
```bash
$ git stash clear
```

### git stash show
查看堆栈中最新保存的stash和当前目录的差异。
```bash
$ git stash show

# 查看指定的stash和当前目录差异。 
$ git stash show stash@{1}

# 查看详细的不同
$ git stash show -p

# 查看指定的stash的差异内容
$ git stash show stash@{1} -p
```
![](/images/git/32.png)

### git stash branch
从最新的stash创建分支，与pop一样，会删除stash。
```bash
# 根据最近的stash创建
$ git stash branch [branch-name]

# 根据指定的stash创建
$ git stash branch [branch-name] stash@{1}
```
![](/images/git/33.png)

应用场景：当储藏了部分工作，暂时不去理会，继续在当前分支进行开发，后续想将stash中的内容恢复到当前工作目录时，如果是针对同一个文件的修改（即便不是同行数据），那么可能会发生冲突，恢复失败，这里通过创建新的分支来解决。可以用于解决stash中的内容和当前目录的内容发生冲突的情景。 
发生冲突时，需手动解决冲突。

## 标签

### 列出所有标签
```bash
$ git tag
```

### 新建标签
```bash
# 在当前commit新建一个tag
$ git tag [tag]

# 在指定commit新建一个tag
$ git tag [tag] [commit]
```
![](/images/git/34.png)

### 查看标签
```bash
$ git show [tag]
```
![](/images/git/35.png)

### 删除本地标签
```bash
$ git tag -d [tag]
```
![](/images/git/36.png)

### 推送标签
```bash
# 推送指定标签到远程仓库
$ git push [remote] [tag]

# 推送所有标签到远程仓库
$ git push [remote] --tags
```
![](/images/git/37.png)

### 查看远程标签
```bash
$ git ls-remote –-tags [remote]
```
![](/images/git/38.png)

### 删除远程标签
```bash
# 先删除本地标签
$ git tag -d [tag]

# 推送到远程仓库
$ git push [remote] :refs/tags/[tag]
```
![](/images/git/39.png)

## 冲突
