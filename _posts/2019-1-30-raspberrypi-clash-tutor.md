---
layout: post
title: "在树莓派上配置Clash-linux"
date:  2019-01-30 13:59:33 +0800
category: Technology
tags: IoT Linux GFW 
img: https://pic2.zhimg.com/v2-decd28d5fe7ebf988dc79e22d080ed09_1440w.jpg?source=172ae18b
describe: 树莓派把家里的灰都吸走啦！
---

## 前言

一直在折腾家里的路由器和相关网络设备，想提供一个较为完美的网络环境。之前在Phicomm K2P上通过Openwrt安装luci-ssr-plus来进行国外ip的代理，但受限于简单的规则和MT7261令人捉鸡的性能，体验不佳。因此萌生了使用树莓派搭建透明网关的想法。在查阅了为数不多的教程并踩了很多坑以后基本搭建完成，并且到目前还未出现问题，所以说说如何使用。

## 准备工作

- 树莓派
- 正常的网络连接
- 一定的动手和解决问题的能力

## Clash

> [Clash](https://github.com/Dreamacro/clash) is a rule-based tunnel in Go.

Clash 类似 IOS/Mac OS上的Surge，可以在提供SS/V2RAY代理的同时资瓷自定义的代理规则。



### 编译

虽然说[Clash](https://github.com/Dreamacro/clash)的项目主页说你可以通过`go get -u -v github.com/Dreamacro/clash`的方式构建，但是因为要去Google服务器上下载包而变得困难；项目主页的[预先构建](https://github.com/Dreamacro/clash/releases)并不支持ARM架构的树莓派，因此需要自行编译。

这里[@shinohara-rin](https://github.com/shinohara-rin) 构建了 Clash-arm (v0.10.2) ，[点击这里](https://transfer.sh/V5YQg/clash)下载。

### 配置

首先将clash文件移动/下载到树莓派的目录下，然后移动到 `/usr/local/bin`，并给予权限。
```bash
# 把解压的二进制放到 /usr/local/bin 目录下
$sudo mv ./clash /usr/local/bin

#给予权限
$chmod 555 /usr/local/bin
```

关于配置方法，[Github的项目主页](https://github.com/Dreamacro/clash)有详细的说明，在这里简单说一下我的配置。

```bash
#运行Clash
$clash
```

正常的话会提示

```bash
INFO[0000] Can't find config, create a empty file
INFO[0000] Can't find MMDB, start download
FATA[0005] Parse config error: Configuration file /home/pi/.config/clash/config.yml is empty
```

这个时候需要编辑 `/home/pi/.config/clash/config.yml`

(由于我们把配置放到了默认的位置, 因此可以不用加 `-d` 参数, 可以使用 `clash` 直接启动, )

如下是我的配置（参考）

```bash
# port of HTTP
port: 7890

## port of SOCKS5
socks-port: 7891

# `allow-lan` must be true in your config.yml
allow-lan: true

# set log level to stdout (default is info)
# info / warning / error / debug / silent
log-level: info

# A RESTful API for clash
#使用0.0.0.0可以使用局域网设备访问
external-controller: 0.0.0.0:8080

mode: Rule

Proxy:
#以下省略，由梯子的服务商提供
```

如果没有问题，下面是回复

```bash
$ clash
INFO[0002] RESTful API listening at: 0.0.0.0:8080
INFO[0002] SOCKS proxy listening at: 127.0.0.1:7891
INFO[0002] HTTP proxy listening at: 127.0.0.1:7890
```

到这里Clash算是正常运行了，[Github的项目主页](https://github.com/Dreamacro/clash)推荐使用PM2守护进程。

### 一些错误及解决方法

- 如果运行Clash后没有回应：尝试编辑`config.yml`

- 无法执行二进制文件：使用兼容ARM的Clash版本或重新编译

- 没有权限执行：用`chmod`命令赋权

### 面板控制

为了使用图形化的界面管理代理规则。我使用了[yacd面板](https://github.com/haishanh/yacd)

使用方法：

- [打开网页]([http://yacd.haishan.me](http://yacd.haishan.me/))
- 填入配置（`ip`为树莓派所在的内网地址，例如`192.168.2.14`,端口为`config.yml`所设置的`8080`）
- 完成

### 参考

- [Linux 配置科学上网](https://www.shangzongyu.com/post/tools/linux-over-gfw/)

- [在树莓派上使用kone和clash]( https://beyondkmp.com/post/kone_clash/)
- [Clash Linux 代理使用](https://tech.viewv.top/2019/01/23/Clash-Linux.html)