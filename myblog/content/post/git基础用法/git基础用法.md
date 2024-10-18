---
title: Git基础用法
description: 持续更新中
date: 2024-10-16T21:15:12+08:00
slug: Git基础用法
image: https://pic.netbian.com/uploads/allimg/231016/223346-16974668265cf4.jpg
categories:
  - git
  - 基础
---
# Git基础用法
## Git 配置

- 设置用户名
```
git config --global user.name "Your Name"
```

- 设置邮箱
```
git config --global user.email "you@example.com"
```
-  查看配置信息
```
git config --list
```
## Git 仓库

- 初始化一个新的 Git 仓库
```
git init
```
- 克隆远程仓库
```
git clone <repository-url>
```
## 文件操作
   

- 添加文件到暂存区（Staging Area）
```
git add <file>        - 添加单个文件
git add .             - 添加所有修改的文件
```
- 提交到本地仓库
```
git commit -m "Commit message"
```
- 删除文件
```
git rm <file>
```
- 重命名文件
```
git mv <old-filename> <new-filename>
```
## 查看状态与历史
- 查看当前工作区状态
```
git status
```
- 查看提交历史
```
git log
```
- 查看提交记录简洁版本
```
git log --oneline
```
## 分支操作


- 查看分支列表
```
git branch
```
- 创建新分支
```
git branch <branch-name>
```
- 切换到指定分支
```
git checkout <branch-name>
```
- 创建并切换到新分支
```
git checkout -b <branch-name>
```
- 合并分支
```
git merge <branch-name>
```
- 删除分支
```
git branch -d <branch-name>
```
## 远程仓库操作


- 查看当前远程仓库
```
git remote -v
```
- 添加远程仓库
```
git remote add origin <repository-url>
```
- 从远程仓库拉取代码
```
git pull origin <branch-name>
```
- 推送代码到远程仓库
```
git push origin <branch-name>
```
## 撤销与回退


- 撤销工作区修改
```
git checkout -- <file>
```
- 撤销暂存区的修改
```
git reset <file>
```
- 回退到上一个提交版本
```
git reset --hard HEAD^
```
- 回退到某个提交版本
```
git reset --hard <commit-id>
```