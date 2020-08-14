---
title: 也谈科学上网
date: 2019-08-04 21:09:35 +0800
category: Technology
tags: GFW Linux
excerpt: 这条线路又双叒叕挂了
---



### 前言
​			关于科学上网，网上的教程已经数不胜数了。从之前的SS,SSR一键脚本再到如今新兴的V2ray。俺也从原本的GoAgent到赛风再到自建再到机场一步步摸爬滚打了过来。在这里谈一谈俺的看法。



### CAUTION

- 本文具有**时效性**
- **使用脚本有一定风险性，请阅读源码后食用**



### 为什么选择V2ray

​				评判科学上网的好坏有很多因素，延迟、速度、稳定。用到现在发现稳定其实是最重要的。在一年一度的儿童节来临之际，V2ray出人意料的坚挺，而且V2ray有很多新的特性。~~反观SS\SSR，在破娃退坑之后几乎没有新变化~~。墙会检测大流量的，通往境外的加密流量，我认为V2ray的Websocket+TLS能起到一定的伪装作用，在一定程度上避免QOS，~~虽然提速可能比较慢~~。



### 配置

俺比较懒，使用了[233boy](https://github.com/233boy)的[一键脚本](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)。

首先运行命令**（以root模式运行）**

```shell
	sudo su
	bash <(curl -s -L https://git.io/v2ray.sh)
```
接着会出现

  ```shell
    ........... V2Ray 一键安装脚本 & 管理脚本 by 233v2.com ..........
    
    帮助说明: https://233v2.com/post/1/
    
    搭建教程: https://233v2.com/post/2/
    
    1. 安装
    2. 卸载
  ```

显然选择`1`

```shell
请选择 V2Ray 传输协议 [1-32]

1. TCP
2. TCP_HTTP
3. WebSocket
4. WebSocket + TLS
5. HTTP/2
6. mKCP
7. mKCP_utp
8. mKCP_srtp
9. mKCP_wechat-video
10. mKCP_dtls
11. mKCP_wireguard
12. QUIC
13. QUIC_utp
14. QUIC_srtp
15. QUIC_wechat-video
16. QUIC_dtls
17. QUIC_wireguard
18. TCP_dynamicPort
19. TCP_HTTP_dynamicPort
20. WebSocket_dynamicPort
21. mKCP_dynamicPort
22. mKCP_utp_dynamicPort
23. mKCP_srtp_dynamicPort
24. mKCP_wechat-video_dynamicPort
25. mKCP_dtls_dynamicPort
26. mKCP_wireguard_dynamicPort
27. QUIC_dynamicPort
28. QUIC_utp_dynamicPort
29. QUIC_srtp_dynamicPort
30. QUIC_wechat-video_dynamicPort
31. QUIC_dtls_dynamicPort
32. QUIC_wireguard_dynamicPort

备注1: 含有 [dynamicPort] 的即启用动态端口..
备注2: [utp | srtp | wechat-video | dtls | wireguard] 分别伪装成 [BT下载 | 视频通话 | 微信视频通话 | DTLS 1.2 数据包 | WireGuard 数据包]

(默认协议: TCP):4

```

在这里选择`4`

```shell
----------------------------------------------------------------

请输入 V2Ray 端口 [1-65535]，不能选择 80 或 443 端口
(默认端口: 54869): your port

```

将这里的`your port`替换乘你想要的端口（这取决于你ISP的限制，比如某些ISP可能对高位/低位端口有所限制）

```shell
V2Ray 端口 = your port
----------------------------------------------------------------


请输入一个 正确的域名，一定一定一定要正确，不！能！出！错！
(例如：233blog.com): your domain


 你的域名 = your domain
----------------------------------------------------------------


 请将 your domain 解析到: your ip

 请将 your domain 解析到: your ip

 请将 your domain 解析到: your ip
----------------------------------------------------------------

(是否已经正确解析: [Y]): 
```

因为协议是Websocket，所以一个指向你server ip的域名是必不可少的

将`your domain` 替换为你的域名，将`your ip`替换为你服务器的ip

如果没有域名的话可以去 [dot.tk](dot.tk) 免费申请，并建议用第三方dns解析服务商托管，这里不再赘述。

```shell
(是否已经正确解析: [Y]): Y


 域名解析 = 我确定已经有解析了
----------------------------------------------------------------



		安装 Caddy 来实现 自动配置 TLS
		
		如果你已经安装 Nginx 或 Caddy

		并且..自己能搞定配置 TLS

		那么就不需要 打开自动配置 TLS
		
----------------------------------------------------------------

(是否自动配置 TLS: [Y/N]): 
Y
----------------------------------------------------------------


是否开启 网站伪装 和 路径分流 [Y/N]
(默认: [N]):N


网站伪装 和 路径分流 = 不想配置
 
----------------------------------------------------------------

是否开启广告拦截(会影响性能) [Y/N]
(默认 [N]):Y


 广告拦截 = 开启
----------------------------------------------------------------

是否配置 Shadowsocks [Y/N]
(默认 [N]): N

 ....准备安装了咯..看看有毛有配置正确了...

----------------------------------------------------------------


---------- 安装信息 -------------

 V2Ray 传输协议 = WebSocket + TLS

 V2Ray 端口 = 344

 你的域名 = v.aaaab3n.co

 域名解析 = 我确定已经有解析了

 自动配置 TLS = 打开

 广告拦截 = 开启

 是否配置 Shadowsocks = 未配置



按 Enter 回车键 继续....或按 Ctrl + C 取消.

```



配置如图，按`Enter键`继续，耐心等待即可。

本脚本会自动安装bbr单边加速

完成之后便可查看配置信息



```shell

---------- V2Ray 配置信息 -------------

 地址 (Address) = your domain

 端口 (Port) = 443

 用户ID (User ID / UUID) =  your uuid

 额外ID (Alter Id) = 233

 传输协议 (Network) = ws

 伪装类型 (header type) = none

 伪装域名 (host) = your domain

 路径 (path) = /

 TLS (Enable TLS) = 打开

 备注: 广告拦截已开启..

---------- END -------------
```



### 连接

此处以`Quantumult`和`Clash`为例

![IMG_0965 (2)](\assets\img\2019\IMG_0965 (2).png)

![clash](\assets\img\2019\clash.png)



### One More Thing

- [Clash for Windows](https://github.com/Fndroid/clash_for_windows_pkg/releases)
- [Quantumult](https://apps.apple.com/us/app/quantumult/id1252015438)
- 由于TLS的特性，vps时区调到和本地一样还要校准时间。
- 注意检查server防火墙的设置
- **注意风险**

### 参考
- [V2Ray一键安装脚本](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)
- [WebSocket+TLS+Web](https://toutyrater.github.io/advanced/wss_and_web.html)