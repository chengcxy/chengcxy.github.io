
---
title: "使用hugo发布github pages网站"
---
# 使用hugo发布github pages网站

[Hugo](https://gohugo.io/) is the world’s fastest static website engine. It’s written in Go (aka Golang) and developed by bep, spf13 and friends.

![Image alt](media/hugo_logo.jpg)

## 步骤

安装 hugo

```terminal
brew install hugo
# or
port install hugo
```

创建本地站点

```terminal
hugo new site chengcxy.github.io
```

导入 theme 我喜欢 hugo-book

```terminal
cd chengcxy.github.io
git init
git submodule add https://github.com/alex-shpak/hugo-book themes/hugo-book
```

写入配置，config.toml

```toml
theme = "hugo-book"
```

在github上新建仓库 chengcxy.github.io，关联本地站点

```terminal
git remote add origin git@github.com:chengcxy/chengcxy.github.io.git
```

创建 github action 脚本

.github/workflows/gh-pages.yml

```yml
# Sample workflow for building and deploying a Hugo site to GitHub Pages
name: Deploy Hugo site to Pages

on:
  # Runs on pushes targeting the default branch
  push:
    branches: ["main"]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow one concurrent deployment
concurrency:
  group: "pages"
  cancel-in-progress: true

# Default to bash
defaults:
  run:
    shell: bash

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.102.3
    steps:
      - name: Install Hugo CLI
        run: |
          wget -O ${{ runner.temp }}/hugo.deb https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_Linux-64bit.deb \
          && sudo dpkg -i ${{ runner.temp }}/hugo.deb
      - name: Checkout
        uses: actions/checkout@v3
        with:
          submodules: recursive
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v2
      - name: Build with Hugo
        env:
          # For maximum backward compatibility with Hugo modules
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          hugo \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
        with:
          path: ./public

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v1

```

提交代码

```terminal
git add .
git commit -m "init site"
```

打开站点 https://chengcxy.github.io/ 完成。

## hugo中文文档

[用hugo搭建github免费站点](https://www.gohugo.org/)