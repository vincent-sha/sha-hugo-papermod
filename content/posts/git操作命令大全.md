---
author: 文森
title: Git常用命令详解与实用指南
date: 2025-11-28
lastmod: 2025-11-28
slug: git-common-commands-guide
draft: false
description: 本文系统整理了Git的常用命令，包括初始化仓库、配置用户信息、分支管理、代码提交、查看日志、远程操作、文件管理等，帮助用户快速掌握Git版本控制的基本操作与进阶用法，提高代码管理效率。
categories:
  - 技术教程
series:
  - github
tags:
  - git
  - 命令
  - 指南
cover:
  image: images/github-cover-001.jpg
ShowToc: true
ShowBreadCrumbs: true
comments: true
---

# Git 常用命令详解与实用指南

本文总结了Git的核心命令，涵盖从初始化仓库到远程协作的各个方面，帮助你高效管理代码版本。

---

## 初始化与配置

```bash
# 初始化本地Git仓库
git init

# 配置全局用户名和邮箱
git config --global user.name "你的用户名"
git config --global user.email "你的邮箱"

# 启用命令输出颜色，提升可读性
git config --global color.ui true
git config --global color.status auto
git config --global color.diff auto
git config --global color.branch auto
git config --global color.interactive auto

# 取消HTTP代理设置
git config --global --unset http.proxy

```

## 克隆与远程仓库操作

```bash
# 克隆远程仓库
git clone git+ssh://git@192.168.53.168/VT.git

# 添加远程仓库地址
git remote add origin git+ssh://git@192.168.53.168/VT.git

# 查看远程仓库分支
git branch -r

# 获取远程分支数据（不自动合并）
git fetch

git fetch --prune  # 同时清理远程已删除分支

# 拉取远程分支并合并到当前分支
git pull origin master

# 推送当前分支到远程仓库
git push origin master

# 推送所有标签
git push --tags

# 删除远程分支
git push origin :hotfixes/BJVEP933

```

---

## 版本状态查看与文件管理

```bash
# 查看当前仓库状态
 git status

# 将文件添加到暂存区
 git add xyz
 git add .  # 添加当前目录所有变更文件

# 提交改动
 git commit -m '提交信息'

# 修改上一次提交信息
 git commit --amend -m '新的提交信息'

# 一步完成添加并提交（仅限已跟踪文件）
 git commit -am '提交信息'

# 删除文件（同时从暂存区和工作区删除）
 git rm 文件名

# 递归删除当前目录所有文件
 git rm -r *

# 重命名文件
 git mv README README2

# 查看Git索引包含的文件
 git ls-files

```

---

## 日志查看与版本比较

```bash
# 显示提交日志
 git log

# 显示最近1条日志
 git log -1

# 显示最近5条日志
 git log -5

# 显示日志及相关文件变动统计
 git log --stat

# 显示详细的提交差异
 git log -p -m

# 查看指定提交的详细信息
 git show dfb02e6

# 查看最新提交
 git show HEAD

# 查看上一个版本提交
 git show HEAD^

# 查看标签信息
 git tag

git tag -a v2.0 -m '版本说明'  # 创建带注释的标签

# 查看标签的提交信息
 git show v2.0

# 查看标签的日志
 git log v2.0

# 显示未暂存的文件差异
 git diff

# 显示已暂存但未提交的变更
 git diff --cached

# 比较与上一个版本的差异
 git diff HEAD^

# 比较指定目录的差异
 git diff HEAD -- ./lib

# 比较远程分支与本地分支差异
 git diff origin/master..master

# 只显示差异文件列表，不显示内容
 git diff origin/master..master --stat

```

---

## 分支管理

```bash
# 查看本地分支
 git branch

# 查看所有本地和远程分支
 git branch -a

# 显示所有远程分支
 git branch -r

# 显示包含特定提交的分支
 git branch --contains 50089

# 显示已合并到当前分支的分支
 git branch --merged

# 显示未合并到当前分支的分支
 git branch --no-merged

# 本地分支重命名
 git branch -m master master_copy

# 创建并切换到新分支
 git checkout -b master_copy

# 从远程分支创建本地新分支并切换
 git checkout -b devel origin/develop

# 切换到已有分支
 git checkout features/performance

# 检出远程分支并建立跟踪
 git checkout --track hotfixes/BJVEP933

# 切换到标签版本
 git checkout v2.0

# 删除本地分支（已合并）
 git branch -d hotfixes/BJVEP933

# 强制删除本地分支（未合并）
 git branch -D hotfixes/BJVEP933

```

---

## 合并与变基

```bash
# 合并远程master分支到当前分支
 git merge origin/master

# 选择性合并某次提交
 git cherry-pick ff44785404a8e

# 变基操作
 git rebase

```

---

## 重置与回滚

```bash
# 硬重置到HEAD版本（丢弃未提交修改）
 git reset --hard HEAD

# 撤销指定提交的影响（生成新提交）
 git revert dfb02e6e4f2f7b573337763e5c0013802e392818

# 检出某个文件回到HEAD版本（恢复文件）
 git checkout -- README

```

---

## 暂存区操作（stash）

```bash
# 暂存当前修改，恢复工作区为HEAD状态
 git stash

# 查看暂存列表
 git stash list

# 显示某次暂存内容
 git stash show -p stash@{0}

# 应用某次暂存内容
 git stash apply stash@{0}

```

---

## 其他实用命令

```bash
# 搜索文件内容包含"delete from"的文本
 git grep "delete from"

# 使用多个条件搜索
 git grep -e '#define' --and -e SORT_DIRENT

# 垃圾回收，优化仓库空间
 git gc

# 检查仓库完整性
 git fsck

# 查看分支历史图示
 git show-branch
 git show-branch --all

# 查看提交历史对应的文件修改
 git whatchanged

# 查看所有提交记录（包括孤立提交）
 git reflog

# 查看HEAD@{5}的状态
 git show HEAD@{5}

# 查看master分支昨天的状态
 git show master@{yesterday}

# 图示日志简洁视图
 git log --pretty=format:'%h %s' --graph

# 查看特定提交的原始详细信息
 git show -s --pretty=raw 2be7fcb476

```

---

通过熟练掌握以上Git命令，可以极大提高代码管理效率，轻松应对团队协作中的各种版本控制需求。

---

> 提示：
> 
> - 命令中的"xxx"和示例地址请替换为实际的用户名、邮箱和仓库地址。
> - 使用命令前建议先了解各命令的具体作用，避免误操作造成数据丢失。