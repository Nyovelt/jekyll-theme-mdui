---
title: Remote-SSH:我的开发环境
date: 2020-3-24 9:00:59 +0800
category: Technology
tags: Monthly-Report
excerpt: 让surface真正好用起来
---

# Remote-SSH: 我的开发环境

Visual Studio Code 在之前发布了插件 remote-ssh 和 remote-wsl 作为vsc的远程开发套件。这两款插件对于 Linux 的支持非常的好，<del>甚至有人认为这是Windows下最好的ssh工具</del>。我目前的主力设备是HP-OMEN 2 (i5-6300HQ) 和 Surface Pro 6 (i5) 。为了用上好用的包管理器，我都是装了Arch的Windows Subsystem Linux 作为日常开发。

在最近，随着天气转暖，以及经手了几个稍微复杂的项目部署，wsl相对于cpu的占用率经常达到了50%左右，明显感受到了性能瓶颈，外加hyper的内存泄漏，，<del>让我周一的 python 在线 quiz雪上加霜</del>。更不用说surface贫弱的性能和移动性，不适合在本地跑大项目。

在半年前我刚上大学的时候，xa学长就向我推荐了remote-ssh 。可以充分利用阿里云学生机 9.9 块的羊毛，外加原生的 linux 在编译速度上玄学的快于 windows ( 在编译TeX时，有明显的感觉 )的特性。所以在最近我将开发环境转移到了阿里云。经过了一周的试用，我认为这是目前最适合我的使用场景的开发环境。首先，它解决了代码多设备同步的问题；其次，它分担了 surface 的压力 ；最后， surface 因此成为了我的便携式个人电脑。

当然，如果没有阿里云学生机或者其它VPS，一块树莓派加 Zerotier 也应该是可以的。

## Set-UP

首先，**颜值既是生产力**。因此首先在Linux主机上安装并配置oh-my-zsh。

<img src="https://p5psmq.sn.files.1drv.com/y4mrWO7Fa2_wX0-3WrjWB1n63xJX658Htwt_X0CCedBV-JiUNn_3t9uYmUiQHAgzuSqdbBXTpKXMHSdlLp7YrdKbF2HeFfT8qhhFUlypYJolt7TzcovJp3TJ87s6liK6pqWeGFj0S2ljMN3H2YIz9g1ZNKH_-p4a7IhZYPQRhY-qjGu13Yw18PpwJ6ccCPHkT5OBJzGLRxVxMIVL3B-zEid-g?width=1472&amp;height=898&amp;cropmode=none" alt="https://p5psmq.sn.files.1drv.com/y4mrWO7Fa2_wX0-3WrjWB1n63xJX658Htwt_X0CCedBV-JiUNn_3t9uYmUiQHAgzuSqdbBXTpKXMHSdlLp7YrdKbF2HeFfT8qhhFUlypYJolt7TzcovJp3TJ87s6liK6pqWeGFj0S2ljMN3H2YIz9g1ZNKH_-p4a7IhZYPQRhY-qjGu13Yw18PpwJ6ccCPHkT5OBJzGLRxVxMIVL3B-zEid-g?width=1472&amp;height=898&amp;cropmode=none" style="zoom:50%;" />

<center>Progynova 9.12 </center>

运行命令安装 zsh :

```shell
sudo apt install zsh
```

之后再安装 oh-my-zsh :

curl 安装 :

```shell
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

wget安装 :

```shell
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

设置 zsh 为默认shell :

```shell
# 为root用户修改默认shell为zsh
chsh -s /bin/zsh root
# 为当前用户修改默认shell为zsh
chsh -s /bin/zsh
```

这是 oh-my-zsh 的配置文件路径: `~/.zshrc`

然后更改配置以更换主题: 

```shell
nano ~/.zshrc
```

更换主题:

```json
#如果是服务器我推荐：
ZSH_THEME="sorin"
#如果是个人电脑我推荐：
ZSH_THEME="agnoster"
# agnoster 需要安装 powerline 字体以正确显示
# 推荐字体DejaVu
```

然后安装插件: 

### zsh-syntax-highlighting

[官网](https://github.com/zsh-users/zsh-syntax-highlighting)



**作用**:  平常用的`ls`、`cd` 等命令输入正确会绿色高亮显示，输入错误会显示其他的颜色。

![](https://ooo.0o0.ooo/2017/05/21/5921b54d899d9.png)

**安装**

克隆项目

```shell
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

在 `~/.zshrc` 中配置

```json
plugins=(其他的插件 zsh-syntax-highlighting)
```

使配置生效

```bash
source ~/.zshrc
```



### zsh-autosuggestions

[官网](https://github.com/zsh-users/zsh-autosuggestions)

**作用**

效率神器 👍

如图输入命令时，会给出建议的命令（灰色部分）按键盘 → 补全

![](https://user-gold-cdn.xitu.io/2018/4/25/162fb3812c15aac4?imageView2/0/w/1280/h/960/ignore-error/1)

**安装**

克隆项目

```shell
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions
```

在 `~/.zshrc` 中配置

```json
plugins=(其他的插件 zsh-autosuggestions)
```

使配置生效

```bash
source ~/.zshrc
```



### 镜像源：

如果用的不是阿里云的机器或者是自建的机器（比如树莓派），为了加速访问，需要更换镜像源。

- [交大源](https://mirrors.sjtug.sjtu.edu.cn/#/)   交大作为上海教育网的出口和骨干，也是咱使用的镜像源。在咱这里访问能跑满带宽(12.8M/s)
- [中科大源](http://mirrors.ustc.edu.cn/) 和 [清华源](https://mirrors.tuna.tsinghua.edu.cn/) 老牌镜像源

- 咱维护的 [上科大源](https://mirrors.geekpie.club/)  （不过由于学校带宽紧张，校外出口每个用户限速1M/s , 校内访问可以跑满网卡 ）

## VSCODE 配置与插件推荐

<img src="https://r5qu0w.sn.files.1drv.com/y4m2kBgoK8yjc2cmeqMhSJPQg_BZEtYD8Zf5i095EWWMu0En4Ea3zNS2PfUWFK1V7RyEmRz1yRKzJyX_cAdeuuW3k9bBxKrOhQ0A5C_mBJxQJ0QZPQBGnGBVMITcKbm_Ss_b4s3knx9_u5KyrLZ9cZFIh7BXEO6oh2gVs5x2HkdyF09IsKOSAOU7CtYCDMwYj2loLPgev2wGXW000LqkcKBMA?width=3840&amp;height=2080&amp;cropmode=none" alt="https://r5qu0w.sn.files.1drv.com/y4m2kBgoK8yjc2cmeqMhSJPQg_BZEtYD8Zf5i095EWWMu0En4Ea3zNS2PfUWFK1V7RyEmRz1yRKzJyX_cAdeuuW3k9bBxKrOhQ0A5C_mBJxQJ0QZPQBGnGBVMITcKbm_Ss_b4s3knx9_u5KyrLZ9cZFIh7BXEO6oh2gVs5x2HkdyF09IsKOSAOU7CtYCDMwYj2loLPgev2wGXW000LqkcKBMA?width=3840&amp;height=2080&amp;cropmode=none" style="zoom: 25%;" />



自用的插件：

本地： [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)  [Bracket Pair Colorizer 2](https://marketplace.visualstudio.com/items?itemName=CoenraadS.bracket-pair-colorizer-2)    [Chinese (Simplified) Language Pack for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=MS-CEINTL.vscode-language-pack-zh-hans)   [Settings Sync](https://marketplace.visualstudio.com/items?itemName=Shan.code-settings-sync)  

远程：  [Polacode](https://marketplace.visualstudio.com/items?itemName=pnp.polacode)  [Power Mode](https://marketplace.visualstudio.com/items?itemName=hoovercj.vscode-power-mode) *按个人喜好   [Waka TIme](https://marketplace.visualstudio.com/items?itemName=WakaTime.vscode-wakatime) 

主题:    [Dracula Official](https://marketplace.visualstudio.com/items?itemName=dracula-theme.theme-dracula)



### Remote-SSH的设置

点击右下角，之后选择 `Remote-SSH: Connect to host` 

<img src="https://p5plmq.sn.files.1drv.com/y4m0njtbxsLnNscL9dhR7Mj0dmT5DvIFz92sz6graOKjwNOVk_CAYSYHC1ZbEEEl800sKAwWFmYNYwqqTz7BwuTCWnpRVa4oC60-SdUDoqjL2oaIT6kS1U9laZorQLcld3qKo5T_Xmlei44wnVDSESuD4WAYmN4hWlPR994AJEXB-Wp7-D9lgxfv1Y8iE3MA2Gi8yEO5UZt0LlH3lXVzyMZZw?width=2736&amp;height=1744&amp;cropmode=none" alt="https://p5plmq.sn.files.1drv.com/y4m0njtbxsLnNscL9dhR7Mj0dmT5DvIFz92sz6graOKjwNOVk_CAYSYHC1ZbEEEl800sKAwWFmYNYwqqTz7BwuTCWnpRVa4oC60-SdUDoqjL2oaIT6kS1U9laZorQLcld3qKo5T_Xmlei44wnVDSESuD4WAYmN4hWlPR994AJEXB-Wp7-D9lgxfv1Y8iE3MA2Gi8yEO5UZt0LlH3lXVzyMZZw?width=2736&amp;height=1744&amp;cropmode=none" style="zoom:50%;" />

之后选择 Configure SSH Hosts 打开配置文件进行配置

![](https://p5pmmq.sn.files.1drv.com/y4mpR_EFGw1mP18ZlzzCTwTq6WJWTiUEKM7sIIeO8R0sBFuZdZ-t1nuRbceom18yme8pJ8RZ-fIALlve7GWQtScVpBYZfy31cMEYW_3OgbKSun-7Qc1tg1KBe_KQalnYkMy6L3rCQavbxEUVz3BKRlbK-t8P6ij5TLGzkaLOuFtJl503BHVgTYUh2zp2b74qaGqu64RQxJO7-w4OccM6wNtug?width=2736&height=1744&cropmode=none)

`ssh config` 的配置方式如下: 

```json
Host {your host name}
	Hostname {alias or ip or address}
	User {username}
	IdentityFIle {The path of your private key} #非必填项
	ForwardAgent {yes/no} #密钥的多机器转发，非必填项
```



## 一些例子

### Python

在ssh中执行:

```bash
sudo apt install python3
```

然后安装 [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 插件

即可运行:

<img src="https://p5pnmq.sn.files.1drv.com/y4mginpxGISWTmAMyrdwIWbFpEt7cTbHfUfJ8KBYLVvdgukJfNkUFI_t1Eqxr-zZdWDt62gKaNcIG_Ct9JAHMqqZSLK5qduvgB-_O4K_aDjjVm6OgLLLYuV-Ty0ACCYDaQd7CVWIleKIn-lCx2zETKNLscO2hnIy7qGbIK4jI3vihMcRm_0YZo42ogSN1TRjFDUtft27cZEKNEgDmlMdMlXFg?width=2736&amp;height=1752&amp;cropmode=none" alt="https://p5pnmq.sn.files.1drv.com/y4mginpxGISWTmAMyrdwIWbFpEt7cTbHfUfJ8KBYLVvdgukJfNkUFI_t1Eqxr-zZdWDt62gKaNcIG_Ct9JAHMqqZSLK5qduvgB-_O4K_aDjjVm6OgLLLYuV-Ty0ACCYDaQd7CVWIleKIn-lCx2zETKNLscO2hnIy7qGbIK4jI3vihMcRm_0YZo42ogSN1TRjFDUtft27cZEKNEgDmlMdMlXFg?width=2736&amp;height=1752&amp;cropmode=none" style="zoom:50%;" />

不论是 Linux 还是 MacOS ，相对于 Powershell 的优势都在于可以输入 "<" 和 ">" 。对于程序测试来说，显然更加方便。



### TeX

首先安装 `tex-full `

```bash
sudo apt install texlive-full
```

然后安装插件 [LaTeX Workshop ](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)

![](https://p5pomq.sn.files.1drv.com/y4mvxJll2DUrJ8_H5ceAXG406s47HcBKU5A9Lz5Ipj_9itqaXyHff4hK9e45sFZK7xNKXVMlXGdsxyL3PKsMsfNRYBqmPNDfGxHEeSwsmGFYXZkFYkJUFPx126TeVy4OKkOzu6qQnKC7i0JO_fCSxurua9oCEEqz9IS4BSiKakiwhNHuo4i7lVd6MtYYTuEervHcm6j65VBpVSWMazwz6Uchg?width=2736&height=1722&cropmode=none)

编译起来不是一般的快😎

![](https://p5pxmq.sn.files.1drv.com/y4mUuhjrUlBhnvGuQR-ufyO5HREJbdLuXxFPzM85AXmZbovYew6_yuh4ddFMG721xRiebvNoDnFSGN15hrrtG0pewX-KjICiMsXPlZsf8AEV-ZSgD5iA8rVcgrkCIJdGCIKc5MhJWbcyf0SeDfjR9O0fp-u-_5vDo9Xcj4yUGLl7WTg3qM1FPlxaaFwo85_7KiBRlONkrI-AViYdiM6RPAvow?width=2736&height=1754&cropmode=none)

把文件下载到本地也非常方便:

![](https://p5pymq.sn.files.1drv.com/y4m4_9V2--khhyOURXpfkD_41FwR_riViROb_Ik1zNPAQKSjiBBuAv-Y4yxixU3drmvRc2XN167jDN_yGE8w_47vGCHs_1XOxzi1WJGVloLdwhQjhqf2K1O-nEnvorT3o1-z5qlfP23V9iavfdKh3TDOwUvIgnFIfNQdgen5wOJve30mvh-k6H-hYRBZOZYJtWxwJ2ik5NlGZSd2gX4uwhlbA?width=2730&height=1730&cropmode=none)



## 总结

当然，除了上文所提到了以外，还有其它的配置( 比如添加自己的 ssh-key 来完成 git 同步等)可供自定义。

支持远程编译/本地改动的 IDE /编辑器不止VSC一家。对于便携电脑来说，Remote-SSH 可以减轻电脑压力；对于主力Windows机来说， 可以提供 Linux 下编程的便利，也提供的一种便捷代码同步方式 (而且 linux 下使用 git 操作也更加方便)。同时，只要有 linux 环境和主流架构的机器都可以作为 remote-SSH 的远程机，对于硬核爱好者，你甚至可以在路由器上编译自己的代码。



## 参考

[Oh My Zsh，安装，主题配置方法](https://zhuanlan.zhihu.com/p/35283688)

[zsh oh-my-zsh 插件推荐](https://juejin.im/entry/5ae00e54f265da0b8635ea5c)