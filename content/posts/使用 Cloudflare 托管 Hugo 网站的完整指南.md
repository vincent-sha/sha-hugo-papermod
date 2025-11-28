---
author: 文森
title: 使用 Cloudflare 托管 Hugo 网站的完整指南
date: 2025-10-31
lastmod: 2025-10-31
slug: host-hugo-site-on-cloudflare
draft: false
description: 本文详细介绍了如何利用 Cloudflare 平台托管 Hugo 生成的静态网站，涵盖所需的前提条件、配置文件编写、自动构建脚本编写以及 Cloudflare 与 GitHub 的连接步骤，实现网站的持续部署和自动更新。
categories:
  - 技术教程
series:
  - hugo
tags:
  - 建站
  - 部署
  - cloudflare
cover:
  image: images/cloudflare-cover-001.jpg
ShowToc: true
ShowBreadCrumbs: true
comments: true
---

# Cloudflare托管Hugo网站的完整指南

## 简介

本文将指导您如何将基于Hugo构建的静态网站托管到Cloudflare上，并实现从GitHub仓库的持续自动部署。即使您使用的是GitLab，步骤也大同小异。

---

## 前提条件

在开始之前，请确保完成以下准备工作：

1. 注册并登录您的 [Cloudflare账户](https://dash.cloudflare.com/sign-up)。
2. 注册并登录您的 [GitHub账户](https://github.com/signup)。
3. 在GitHub上为您的项目创建一个新的仓库。
4. 在本地创建Git仓库，并添加远程地址指向GitHub仓库。
5. 在本地Git仓库中创建Hugo站点，并使用 `hugo server` 命令测试站点是否正常运行。

---

## 官方操作步骤

### 1. 创建 `wrangler.toml` 配置文件

在项目根目录创建名为 `wrangler.toml` 的文件，内容如下：

```toml
name = "hosting-cloudflare-worker"
compatibility_date = "2025-07-31"

[build]
command = "chmod a+x build.sh && ./build.sh"

[assets]
directory = "./public"
not_found_handling = "404-page"

```

将name = “替换成部署的worker的名称“，这一步很关键

### 2. 创建自动构建脚本 `build.sh`

在项目根目录创建 `build.sh` 脚本文件，内容如下：

```bash
#!/usr/bin/env bash

#------------------------------------------------------------------------------
# @file
# 构建部署Hugo站点到Cloudflare Worker的脚本
#
# Cloudflare Worker会自动安装所需的Node.js依赖
#------------------------------------------------------------------------------

main() {

  DART_SASS_VERSION=1.93.2
  GO_VERSION=1.25.3
  HUGO_VERSION=0.152.2
  NODE_VERSION=22.20.0

  export TZ=Europe/Oslo

  # 安装Dart Sass
  echo "Installing Dart Sass ${DART_SASS_VERSION}..."
  curl -sLJO "<https://github.com/sass/dart-sass/releases/download/${DART_SASS_VERSION}/dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz>"
  tar -C "${HOME}/.local" -xf "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
  rm "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
  export PATH="${HOME}/.local/dart-sass:${PATH}"

  # 安装Go语言
  echo "Installing Go ${GO_VERSION}..."
  curl -sLJO "<https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz>"
  tar -C "${HOME}/.local" -xf "go${GO_VERSION}.linux-amd64.tar.gz"
  rm "go${GO_VERSION}.linux-amd64.tar.gz"
  export PATH="${HOME}/.local/go/bin:${PATH}"

  # 安装Hugo
  echo "Installing Hugo ${HUGO_VERSION}..."
  curl -sLJO "<https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz>"
  mkdir "${HOME}/.local/hugo"
  tar -C "${HOME}/.local/hugo" -xf "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
  rm "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
  export PATH="${HOME}/.local/hugo:${PATH}"

  # 安装Node.js
  echo "Installing Node.js ${NODE_VERSION}..."
  curl -sLJO "<https://nodejs.org/dist/v${NODE_VERSION}/node-v${NODE_VERSION}-linux-x64.tar.xz>"
  tar -C "${HOME}/.local" -xf "node-v${NODE_VERSION}-linux-x64.tar.xz"
  rm "node-v${NODE_VERSION}-linux-x64.tar.xz"
  export PATH="${HOME}/.local/node-v${NODE_VERSION}-linux-x64/bin:${PATH}"

  # 验证安装情况
  echo "Verifying installations..."
  echo Dart Sass: "$(sass --version)"
  echo Go: "$(go version)"
  echo Hugo: "$(hugo version)"
  echo Node.js: "$(node --version)"

  # 配置Git
  echo "Configuring Git..."
  git config core.quotepath false
  if [ "$(git rev-parse --is-shallow-repository)" = "true" ]; then
    git fetch --unshallow
  fi

  # 构建站点
  echo "Building the site..."
  hugo --gc --minify

}

set -euo pipefail
main "$@"

```

### 3. 提交并推送代码

将上述文件加入Git管理，提交并推送到GitHub远程仓库。

### 4. 在Cloudflare中配置

1. 添加Workers

登录Cloudflare控制面板，点击右上角的 **Add** 按钮，选择 **Workers**。

![Cloudflare添加Workers](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-01.png)

1. 开始导入仓库

在Workers页面，点击“Import a repository”旁的 **Get started** 按钮。

![导入仓库](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-02.png)

1. 连接GitHub

![连接GitHub](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-03.png)

选择对应的GitHub账户，授权Cloudflare Workers和Pages访问仓库：

![授权仓库访问](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-05.png)

1. 选择仓库并部署

选择对应的GitHub仓库，配置应用名称，留空构建命令，点击 **Create and deploy**。

![选择仓库](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-06.png)

![创建并部署](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-07.png)

1. 访问网站

部署完成后，您即可访问托管的网站。

![访问网站](http://gohugo.io/host-and-deploy/host-on-cloudflare/cloudflare-08.png)

1. 自定义域名

本地修改hugo.toml中的baseURL，推送更新至GitHub仓库，cloudflare会自动重新部署：

```bash
baseURL = '替换成自定义域名'
```

---

## 最新的部署

根据最新部署情况，因为cloudflare已经内置hugo、go、npm等语言，所以可以省略bulid.sh这一步，新增的wrangler.toml配置文件如下：

```bash
name = "你的cloudflare的worker的名字"
compatibility_date = "2025-07-31"

[build]
command = "hugo --gc --minify" ## 一键命令替代bulid.sh

[assets]
directory = "./public"
not_found_handling = "404-page"

```

## 持续部署

以后每当您在本地Git仓库推送更新到GitHub，Cloudflare会自动重新构建并部署您的网站，实现持续集成和部署。