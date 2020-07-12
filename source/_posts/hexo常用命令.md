---
title: hexo常用命令
date: 2020-07-12 22:07:29
tags: hexo
---

# hexo 常用命令

## 常见命令

### 新建文章

```shell
hexo new "postName"
```

### 新建页面

```shell
hexo new page "pageName"
```

<!-- more -->

### 生成静态页面至public目录

```shell
hexo generate
```

### 开启预览访问

默认端口4000，’ctrl + c’关闭server

```shell
hexo server
```

### 部署到GitHub

```shell
hexo deploy
```

### 查看帮助

```shell
hexo help
```

### 查看Hexo的版本

```shell
hexo version
```

### 缩写

```shell
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

### 组合命令

#### 生成并本地预览

```shell
hexo s -g
```

#### 生成并上传

```shell
hexo d -g
```