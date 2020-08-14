---
title: PIGEON OJ 配置指南（评卷机）
date: 2020-7-7 10:00:59 +0800
category: Technology
tags: Technology
excerpt: 咕咕咕咕咕

---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script> <script type="text/x-mathjax-config"> MathJax.Hub.Config({ tex2jax: { skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'], inlineMath: [['$','$']] } }); </script>

# Pigeon OJ

Pigeon OJ 是由上海科技大学 GeekPie 社团开发的一个基于 Docker 和 Gitlab 的多文件在线评测系统。

在上学期的 CS100 后，该 OJ 已经尘封了一个学期了。 在下个学期的 SI100B 中，该 OJ 也会给新来的 20 届同学使用。在暑学期中， 该 OJ 还要为 专硕 的项目服务。 

OJ 的评卷系统是通过一个 scheduler 通过 TLS 连接 Docker API 调用 Docker 进行评卷。之前的评卷机在 CS100 课程结束后就被学校收回了。 所以现在我需要为其添加一个新的评卷机 ( **Judger** )



![](https://sozp6g.sn.files.1drv.com/y4m0CQRdGXS0RMOHS1FtC6sGvzQujWvZTDq5IT22jkUMLz594GgYa0fFjHPJDtQGppr8rJNFdjir93uz_rFgRGQOtOd8ZGxvfR2nn0s_txH7Jym_OdFBfJSfj7HxOyRy9aQ1h8pRmZuJJflirEwW74yJUjS8lgv4wCQrA5IeL0M5Ivy_Fc55lRfaG3ErtTGFGNPLGFoXCKetdggEmybzw_ipg?width=2736&height=1847&cropmode=none)



  # Docker Remote API TLS 认证

参考链接: [https://www.jianshu.com/p/a1bdc96b4163](https://www.jianshu.com/p/a1bdc96b4163)

这里是我的配置:

```bash
openssl genrsa -aes256 -out ca-key.pem 4096
openssl req -new -x509 -days 365 -key ca-key.pem -sha256 -out ca.pem
openssl genrsa -out server-key.pem 4096
openssl req -subj "/CN={你的域名}" -sha256 -new -key server-key.pem -out server.csr
echo subjectAltName = DNS:{你的域名} > extfile.cnf
openssl x509 -req -days 365 -sha256 -in server.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -extfile extfile.cnf
openssl genrsa -out key.pem 4096
openssl req -subj '/CN=client' -new -key key.pem -out client.csr
echo extendedKeyUsage = clientAuth > extfile.cnf
openssl x509 -req -days 365 -sha256 -in client.csr -CA ca.pem -CAkey ca-key.pem -CAcreateserial -out cert.pem -extfile extfile.cnf
rm -v client.csr server.csr
chmod -v 0400 ca-key.pem key.pem server-key.pem
chmod -v 0444 ca.pem server-cert.pem cert.pem
```

其中， `{你的域名}` 替换为你的域名  (e.g. aaaab3n.oj.geekpie.club)，或者直接使用 ip 。

之后会生成 ca-key.pem key.pem server-key.pem ca.pem server-cert.pem cert.pem

我创建了  ~/.docker 目录并把 `ca.pem`  `server-cert.pem` `server-key.pem` 放入。

之后编辑 /lib/systemd/system/docker.service 

```bash

ExecStart=/usr/bin/dockerd -H unix:///var/run/docker.sock -D -H tcp://0.0.0.0:443 --tlsverify --tlscacert=/home/canary/.docker/ca.pem --tlscert=/home/canary/.docker/server-cert.pem --tlskey=/home/canary/.docker/server-key.pem


```



这里有一个小坑是生成证书的时候我的本地时间没有调整为标准时间，因此之后 TLS 握手时出错。



## 测试

将  `cert.pem`  `key.pem`  `ca.pem` 传输到目标机器，运行命令  `curl https://{你的域名 / IP }:443/info --cert ./cert.pem --key ./key.pem --cacert ./ca.pem`

![](https://sezc6g.sn.files.1drv.com/y4muK2AUHw-sGPOIck-hxy9MEfJV6iDi5ZzhDpOq6MRo0jpA_uYr-5PWuHZepntLGS0Ro94_60ot7lZ5PMqLLEulK352GavemVCQLgoWUokLhr0QfkmNvPI68DtlWAhAzVIf9iz2kn4SOV45JnzULAy0gWXQ8uEJlwPfZrSvm8hH8LAizKf7pxLqPvS5Lwe1wpoaRik3rB6EMLqB99w6fDLjQ?width=1534&height=832&cropmode=none)



# OJ 配置

![](https://sozw6g.sn.files.1drv.com/y4mSwmlVCBWE4cU8f7R3lCgVQmT6K3GdFiXP_LHTyyilo9EoNCuE2FaopSN6Yklwl0p3KQK-YjgjgTRXXg54UxAdn64PgAwIwYmGZl6TWBbZQcMwW5e_uqwG1KlGADYMyIJX5uEmqJPn9l38AP0qQHwgOlspIEsNrTimCHygACUenx6-kURcG-ellxS6SR0cJo-iDd6I_vNSAb2Cdh5pQb9zQ?width=2736&height=1813&cropmode=none)



- Client Key -> key.pem
- Client Cert -> cert.pem
- Cert CA      -> ca.pem 
- MAx job     -> CPU逻辑处理器数

结合之前[一篇文章](https://aaaab3n.co/life/2020/07/07/618-workstation-setup-guide)，我将3900X处理器分配了12个线程给OJ使用。



# 使用感受

![](https://sozu6g.sn.files.1drv.com/y4m1SdXpuAu0otJfNePqn920LfGswI1w1nQTAK_9E9tC_zmjjzshwIlSszWUK9qBWszVdYRU6vP22426OpNWdipN0FT_Rzu2H85qgV1c5GsuddP9YzVMYqvdk6EOB5mzeO05Al8NvtL3PGydUBW3_z5V6TbnP1XRrJWaG2utwz8_7dtCt6CtoTjFfJyV8xPs0RKsF3W9gpAmBiPeN4EVHWoUw?width=2026&height=1188&cropmode=none)



> 就感觉到快，有催人AC的意思