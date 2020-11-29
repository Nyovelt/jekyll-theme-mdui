---
layout: post
title: "使用 GitHub Actions 与 Cloudflare 重构博客体验"
date: 2020-11-28   10:01:59 +0800
category: Technology
tags: Cloudflare Blog Website
img: https://sn3301files.storage.live.com/y4mZYn8vf-p5nrzEvWW-um6hUJ1f8Su2Qo4Yz-LMD9h1h2lk_6Zynu5KmSaN9pgz9H-Sa76WUhuyvurPQ8bfvx4P8XE7qOJGBi_WWAN5g0ycQMLQt7uHc8fNR2hwB54kB7ZgmJSEYcYZBseNJcwc-IvJS93TwbcQMSOLbHaEDqcYHt6ZJ1Fqi2dmkC9NMLbsjT6?width=660&height=440&cropmode=none
describe: Netlify 要收钱了

---

## Caution

Due to rendering problem, some characters can't be displayed correctly by markdown ( **such as $** ). Read source code instead.

## 为什么不自己 Host 服务

正如 苏卡卡 在  [ 谈谈云服务和 SLA ]( https://blog.skk.moe/post/cloud-and-sla/ )  中说的，在线云服务的 SLA 相比自己 Host 一个服务，可用性有很明显的提升。而且 GitHub Pages, GitHub Actions 和 Cloudflare 都是有免费额度的，对于个人用户完全够用。相信很多人之前买了 VPS，之后因为各种原因没有做好备份导致写了很久的博客文章丢失的事情，GitHub 的存储服务也能解决这个问题。



## 为什么不用之前的 Netlify

之前笔者是使用 Netlify 来构建博客的， Netlify 可以 Hook 住某个 GitHub repo， 在有 commit 的时候自动拉下来。它有一个 ubuntu 的虚拟环境，根据写的指令自动构建完后 Host 网页。 Netlify 还有自带的 CDN 和 DNS解析服务，在刚开始搭建博客的时候， Netlify 用的是 Digital Ocean 的 CDN，它的日本节点对于大陆网络的友好程度是要高于 Cloudflare 的。

但最近 Netlify 推出了更加严格的限制，限制了网页流量和编译所用的时长。我一个月后去看发现欠费了 7 美元。再加上现在 DO 的 CDN 对大陆的友好程度直线下降， Cloudflare 在声誉和基础设施上的投入上也使我非常青睐。一个大一同学的项目  [Spedoske/CloudflareScanner (github.com)](https://github.com/Spedoske/CloudflareScanner) 可以扫描出最快的 Cloudflare 节点，有着很多用途。

## GitHub Actions

简单来说，GitHub Actions 可以提供一台无法直接访问的 Azure ，通过 `.github/workflows/*.yml` 执行所写的指令。 Azure 可以提供 Ubuntu 、 Windows 等环境。另一位同学的项目 [KiruyaMomochi/RediveExtract (github.com)](https://github.com/KiruyaMomochi/RediveExtract) 就是使用 GitHub Actions 抓取公主链接的新版本然后解包剧情推送到 Telegram Bot 上。甚至有人用 GitHub Actions 来自动构建 OpenWRT （[Hyy2001X/AutoBuild-Actions: 使用 Github Actions 编译 Openwrt](https://github.com/Hyy2001X/AutoBuild-Actions)）。本文主要介绍了三种框架的 GitHub Actions 。

### Jekyll

Jekyll 在官方 [GitHub Actions ](https://jekyllrb.com/docs/continuous-integration/github-actions/) 提供了 GitHub Actions。不过由于我的依赖和官方的 bundler 版本冲突，所以选择自己写。

该文件见  [jekyll-theme-mdui/.github/workflows at master · Nyovelt/jekyll-theme-mdui](https://github.com/Nyovelt/jekyll-theme-mdui/tree/master/.github/workflows)


```
name: Build and deploy Jekyll site to GitHub Pages

on:
  push:
    branches:
      - master
  workflow_dispatch:
```

- 首先的 name 是整个 GitHub Action 的描述
- on 代表何时触发 Action
  - push 代表当某个分支有提交时触发 push
  - workflow_dispatch 提供了网页手动触发
  - 此外还有每按一次 star 触发 和 schedule 的按时触发

```
jobs:
  github-pages:
    runs-on: ubuntu-latest
    steps:
```

- 表示使用 ubuntu-latest 作为构建机的操作系统，用 docker 的同学应该很熟悉

```
      # Use GitHub Actions' cache to shorten build times and decrease load on servers
      - uses: actions/cache@v1
        with:
          path: vendor/bundle
          key: ${{ runner.os }}-gems-${{ hashFiles('**/Gemfile.lock') }}
          restore-keys: |
            ${{ runner.os }}-gems-
```

- 由于 `bundle install`执行过程时间非常长，所以添加缓存来节省时间和公共资源。该代码块的意思是以每一个 Gemfile.lock 创建一次缓存，这样可以节省安装相应依赖所用的时间

```

      - name: Install dependencies
        run: |
          sudo apt-get update
          sudo apt-get install gem git -y
          sudo gem install bundler:1.17.2 jekyll

      - name: Build jekyll
        run: |
          cd ${{ github.workspace }}
          bundle install
          bundle exec jekyll build
```

- 这两个就和正常 ubuntu 的运行命令一样了。 其中 `${{ github.workspace }}`是 GitHub Actions 的环境变量，表示了 repo 所在的目录

```
      - name: Invoke Deployment
        with:
          ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
          BRANCH: gh-pages
          FOLDER: _site
        uses: JamesIves/github-pages-deploy-action@3.2.1
```

- 这一段代码块是将特定文件夹 `_site`内容推送到 `gh-pages`分支。其中 `secrets.ACCESS_TOKEN`可以在 上文 Jekyll 的文档中找到描述，是在 secrets 里创建一个键值对，来记录你的 GitHub Token 。

### Hugo

```
name: Build and deploy Hugo site to GitHub Pages

on:
  push:
    branches:
      - master
  workflow_dispatch:
  

jobs:
  github-pages:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Install dependencies
        run: |
          cd ~
          wget https://github.com/gohugoio/hugo/releases/download/v0.78.2/hugo_0.78.2_Linux-64bit.deb
          sudo dpkg -i hugo*.deb
          hugo version
           
      - name: Build Hugo
        run: |
          cd ${{ github.workspace }}
          hugo
          
      - name: Invoke Deployment
        with:
          ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
          BRANCH: gh-pages
          FOLDER: public
        uses: JamesIves/github-pages-deploy-action@3.2.1
```

- 默认的 Ubuntu 是 bionic 版本，因此它的软件源不是最新的 （比如 apt 的 Hugo 版本是 0.4 因此不能正常编译）

### Node

- [atomicneko/deploy-pages.yml at master · amphineko/atomicneko (github.com)](https://github.com/amphineko/atomicneko/blob/master/.github/workflows/deploy-pages.yml)

```
name: Deploy to GitHub Pages

on:
  push:
    branches: 
      - master
      - deploy-action
  workflow_dispatch:
  

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
      with:
        persist-credentials: false

    - name: Invoke Webpack build
      run: |
        npm install .
        npm run build

    - name: Invoke Deployment
      with:
        ACCESS_TOKEN: ${{ secrets.ACCESS_TOKEN }}
        BRANCH: gh-pages
        FOLDER: dist
      uses: JamesIves/github-pages-deploy-action@3.2.1

```



## GitHub Pages + Cloudflare

这个部分会介绍的简略一些，因为网上同类文档很多，而且操作很简单。

![](https://sn3301files.storage.live.com/y4mW-R9hfnuB-2UE2bDy4bqoWFMDe08UL1prmOjxt4HU23XIsTj6OMY595LcnBBv7UsmPabV16ys7ZaGxIf7Qm8JUb4FKKwnE3Hy_pCkEe9Cdl24YMt28ecm5BOaMT9NpoK4mDBDKEsxljhCa7Z7uJcHUtB9VUkJ3xMzrXPDJrGgFG7GE6vJLdvmcxOZrC-_pG4?width=1024&height=442&cropmode=none)

总之， Cloudflare 作为 CDN 提供了对网页 （也就是 GitHub Pages）的缓存，同时提供了很多安全选项，比如强制性的 https 和 TLS 。

在从 Netlify 切换到了这一套新的架构以后，访问体验有了肉眼可见的提升。