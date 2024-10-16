---
title: 通过hugo创建个人博客
description: 持续更新中
date: 2024-10-16T21:03:14+08:00
slug: 通过hugo创建个人博客
categories:
  - blog
  - 博客
---

# 通过 hugo 创建个人博客

- [hugo 官网](https://gohugo.io/)
- [hugo 中文文档](https://hugo.opendocs.io/getting-started/)

---

## 一、安装 Hugo

ps: 这里默认已经安装好了 Git、Go等其它工具

1. 进入[hugo 下载链接](https://github.com/gohugoio/hugo/releases/tag/v0.136.0)中下载压缩包，推荐下载扩展版

2. 解压压缩包至自己准备好的文件夹

3. 进入该文件夹，并在命令行中输入以下命令来创建一个新的站点

```
    .\hugo.exe new site myblog
```

4. 创建新站点后就可以进入 myblog 文件夹中运行一下 Hugo 开发服务器来测试是否安装成功

```
    cd myblog
    ..\hugo.exe server -D
```

## 二、添加内容

### 1、添加主题

#### 命令行方式

1. 输入命令添加主题

```
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
```

eg：`https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke`

2. 然后修改 myblog 文件夹中的 config.toml 文件，将主题设置为 ananke

```
theme = "ananke"
```

#### 自行下载方式（推荐）

1. 进入[hugo 官网](https://gohugo.io/)，直接点击**Themes**来选择自己喜欢的主题进行下载
2. 将下载好的压缩包放入 myblog/themes 文件夹中并解压
3. 将配置文件复制到根目录中，替换根目录中原本的配置文件（具体主题之间会有一定差异）
4. 完成后运行查看是否添加成功

#### 一些hugo的命令指令
* 添加新的文章内容
```
.\hugo.exe new content 
```
eg：`.\hugo.exe new content post/通过hugo创建个人博客/通过hugo创建个人博客.md`
* 未完待续
