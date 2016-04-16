---
title: 'git cheatsheet'
guid: 35e30be8a588a70a22674a33533c3ed2
comments: true
layout: post
tags:
     - git
     - cheatsheet
---
参考[廖雪峰大大的教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013758392816224cafd33c44b4451887cc941e6716805c000)先把常用命令抄下来，具体分类和实际应用场景以后再补充。

### 基本命令
 - `git config` 设置git配置文件
 - `git init` 初始化一个新的repo
 - `git add` 添加文件到暂存区
 - `git commit -m <message>` 提交暂存区修改到repo
 - `git status` 查看当前repo状态
 - `git diff` 查看文件修改内容
 - `git log --pretty=oneline` 显示从近到远的提交日志
 - `git reset --hard <commit_id>` 切换到某一个版本
 - `git reflog` 查看命令日志
 - `git checkout -- <file>` 将文件恢复到版本库或暂存区的状态
 - `git reset HEAD <file>` 撤销暂存区修改
 - `git rm file` 从暂存区删除文件，并删除工作区文件
 - `git remote add` 添加远程库
 - `git remote -v` 显示远程库信息
 - `git push origin <branch>` 推送到远程库
 - `git clone` 从远程库克隆
 - `git branch <name>` 创建一个新分支
 - `git chechout <name>` 切换到一个分支
 - `git chechout -b <name>` 创建并切换到一个新分支
 - `git merge <name>` 将某个分支合并到当前分支
 - `git branch -d <name>` 删除某个分支
 - `git merge --no-ff -m <message> <name>` 禁用Fast Forward合并分支
 - `git log --graph --pretty=oneline --abbrev-commit` 查看分支合并历史
 - `git stash` 保存工作场景
 - `git stash list` 列出全部保存的工作场景
 - `git stash apply` 恢复工作场景
 - `git stash drop` 删除保存的工作场景
 - `git stash pop` 恢复并删除某个工作场景
 - `git tag <name> <commit_id>` 创建一个标签（无参数时列出所有标签）
 - `git show <name>` 查看标签信息
 - `git tag -a <name> -m <message> <commit_id>` 创建有描述信息的标签
 - `git push origin <tag_name>` 推送标签到远程
 - `git push origin --tags` 一次性推送全部尚未推送到远程的本地标签
 - `git tag -d <name>` 删除标签
 - `git push origin :refs/tags/<tag_name>` 删除远程标签
 - `git push origin --delete tag <tag_name>` 同上
 - 未完待续。。。