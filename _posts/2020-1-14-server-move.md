---
laout: post
title: "一次服务器迁移的尝试"
date: 2020-1-14 12:59:59 +0800
category: Technology
tags: Linux
img: https://5c8ozw.sn.files.1drv.com/y4mir1p8g0V4gmG9KNPHknVQozqYnvINjDau6z4QA-Hhic8O_-o7AyRw7TVp1Hrp_TTUMnq7sk90rIetYfGota354t-GU2d9cVV5mdH_RBvfYPxBgGncIleToT9gFPnsiD07A_rWTfXfQCJ924l5z8H8IkcQn4661SxgsBTsd4UDav1oJsU0m3coWiWJJfa4NCF3h_YUwDxFgmq-jS2D7Io3w?width=680&height=397&cropmode=none
describe: 踩了无数的坑...
---



## 0x00 缘起

在2019年的某天深夜，所有信院的同学的突然惊恐的发现，交作业的OJ上不去了。<del>然后欣喜的在第二天接到了ddl延期的通知</del>罪魁祸首是...Azure欠费了。



好在作为[叠境]([https://www.dgene.com](https://www.dgene.com/))附属学院，金主爸爸重新赞助了一批阿里云服务器，这批新的服务器将会在2019年双十二期间划归社团。



在没有迁移到阿里云之前，社团暂时由社团经费新开了两台Azure HK，并通过快照的方式及时恢复了服务。费用为11欧/天。



> OJ为社团自建，向全校学生提供服务。 
>
> 该OJ已开源，详见 [https://oj.geekpie.club/about](https://oj.geekpie.club/about )  <del>顺便求star</del>



## 0x01 服务器架构

出于轻量化和弹性化的考虑，我们社团的服务器集群管理用的是 Rancher 1.6 ，所有的服务是纯docker的，我们会封装到docker里，上传到代码托管平台，由CI服务自动build完放到私有registry里，再通过rancher提供给其它服务器使用。



所有的服务器通过 zerotier 组成内网，通过 zerotier 内网访问。




之前，我们一共有三台Azure + 若干台其它server <del>所有服务器都用算法命名</del>

- Azure-CN-E2-0 【brent﻿】            64G        1C2G        负责跑 Rancher 1.6  服务
- Azure-CN-E2-1 【prim﻿】             64G         1C2G        因为服务器在海外，负责 Jenkins自动docker构建和 registry 服务
- Azure-CN-E2-2 【kruskal﻿】         64G         1C2G        负责跑大多数的服务，拥有大量数据库
- etc



叠镜赞助了我们四台服务器：

- 3台上海阿里云 硬盘为 30G  2C2G
- 1台香港阿里云



现在的工作是将 Azure 迁移至 Aliyun ，并保证所有服务都可用。



## 0x02 阿里云官方迁移工具

我们首先查阅了这篇文档

> [Azure虚拟机迁移至阿里云ECS]( https://help.aliyun.com/document_detail/100959.html )



由于阿里云提供了官方的迁云工具，所以这一步稍显简单。



我下载了[迁云工具](https://help.aliyun.com/document_detail/62394.html?spm=a2c4g.11186623.2.18.6c1d7f90FuSH6T#section-twq-sxz-jfb)，解压后首先运行`./Check/client_check --check`命令检查，没有问题，然后配置` user_config.json `。

我的配置是这样的：

```json
{
    "access_id": "YourAccessKeyID",
    "secret_key": "YourAccessKeySecret",
    "region_id": "cn-hangzhou",
    "image_name": "Azure-CN-E2-2",
    "system_disk_size": 70,
    "platform": "Ubuntu",
    "architecture": "x86_64",
    "data_disks": [],
    "bandwidth_limit": 0
}
```

为了确保容量不会有什么问题，我特意做了些冗余，将64G(实际占用54G)的系统盘**做成了70G的快照(image)**。



### 坑点一    系统盘容量问题

大家是否还记得，阿里云的系统盘只开了30G [ QwQ ] 这样做会在快照恢复的时候提示需要多交100多块软妹币将30G系统盘升级到70G。本着**勤俭节约的精神**，我做了如下尝试： 



#### 尝试方案

想利用30G的数据盘，将docker放到数据盘上(docker是大头)：

```json
{
	"access_id" : "YourAccessKeyID",
	"bandwidth_limit" : 0,
	"data_disks" : 
	[
		{
			"data_disk_index" : 1,
			"data_disk_size" : 28,
			"src_path" : "/var/lib/docker"
		}
	],
	"image_name" : "Azure-CN-E2-2-1",
	"platform" : "Ubuntu",
	"region_id" : "cn-shanghai",
	"secret_key" : "YourAccessKeySecret",
	"system_disk_size" : 28
}

```

**结果：** 不明原因失败



#### 解决方案 

某天周五放学去了离学校只有10分钟走路程的叠境，用叠镜的钱加了系统盘，顺便配置了一下防火墙 ( Remark : Zerotier 需要`9000`以上端口的UDP通信 ) 。

<del>然后发现迁移过程中烧掉的流量钱可以扩好几次硬盘了</del>

<del>原来叠境离学校这么近，果然我们是叠境附属学院</del>




## 0x03 Rancher的启动与Zerotier

### 坑点二 Zerotier

由于阿里云的机器是由Azure迁移过去的，所以两者的Zerotier NodeID是一样的。对于Zerotier来说，它只负责建立两者之间的路由，因此你无法判断你到某个阿里云的内网ip会落到阿里云还是Azure。**并且这个路由并不能有效的通过强制刷新解决**。造成了相当程度的内网混乱。

通过**更改NodeID**解决了这个问题。

同时，对于迁移的服务器可以重新配置一个新的内网ip，等完全迁移完成之后改一下ip就行。



### 坑点三 MySQL

对于数据库的复制，要注意权限的对应。如果文件的属性不一样，mysql就可能跑不起来。刚迁移完时，docker跑了五个服务(rancher/server, nginx, ldap, phpmyadmin, mysql) ，其中 MySQL不断重启。



对于rancher来说，分为rancher/server 和 rancher/agent 。 在Rancher 1.6， 所有的数据都被存在数据库中。包括各rancher/agent 的配置。rancher/agent只需要运行 `sudo docker run -e CATTLE_AGENT_IP="[Rancher ip]"  --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v1.2.11 [Rancher domain/ip + id]`就能加入集群。同样的，agent的配置(比如agent里面的docker和环境变量也存在server的数据库中)。只需要再次运行该命令就能重新拉起agent。

由于docker的临时性质（在docker里面的文件会随着docker的结束而结束），我们做了一个映射，将数据映射到`/data`目录，来保存数据库等信息。

因此通过**再一次完整的复制**`/data`解决。



#### 坑点三杠一 文件复制

文件的迁移一共有三种方法。分别是`rsync`, `tar` 和 `dd`。这一次我尝试使用了`rsync` 和`tar`。



一开始使用的是 `rsync` ，因为我们的服务器是通过私钥访问的，所以额外添加了私钥。而mysql数据库文件的迁移需要保证两者文件属性和权限完全一样，所以使用了命令

```bash
$rsync -avzP [username]@[ipaddress]:/home/geekpie/backup
```

最后的错误信息是：

我们的数据是通过 docker 的 -v 参数映射到了根目录下的 /data 文件夹下。 因此在 Azure 上，我们运行了保留文件属性的打包命令:

```bash
$tar cvpzf backup.tgz /data/*
```

然后通过 winSCP <del>基金会</del>把backup.gz下载到个人电脑上再上传到阿里云上去。

运行命令进行复原

```bash
tar xvpfz /home/geekpie/backup.tgz -C /
```

至此，rancher 的数据库以及其它的配置文件就恢复完成了。



#### 坑点三杠二  Rancher连接数据库

参考[链接](https://rancher.com/docs/rancher/v1.6/en/faqs/server/#releasing-the-database-lock)



至此，Rancher就恢复完成了。接下来就是通过 Zerotier 组网，修改域名解析，搭建Zerotier-MOON等工作。负责Rancher的prim和负责各种服务的 kruskal 迁移完成。



从12月初到1月23号，大约两个月的时间，<del>每天11欧</del>，完成了迁移的大部分工作。<del>希望明年社团年夜饭不要自费</del>

## 0x04 Jenkins 和 Portus

大家不一定还记得，我们还有一套自动构建系统 —— Jenkins 和 Portus 。

第一部分的迁移很简单。因为在Rancher上每台主机都有一个lable，因此只要docker的调度不变，重启docker就能跑到新服务器上了。



<del>截止今日 Jenkins 还没有完全恢复</del>





**To be continued.**