---
layout: post
title: 618电脑指北
date: 2020-7-7 9:00:59 +0800
category: Technology
tags: Linux KVM
img: https://7mqcqw.sn.files.1drv.com/y4mZSyMnOmvLD9kDEmPnk4nPBKf51rDYVzw2xXx3ePrmr5WK75GSKbQz3sTyX7ti7DDJrMyY1YNq2KRQZ1Og1gvlRU5uT7dJSSuUl7COTBI2Sjp1BsdSTuK4ZJydywBr6VKy2cJh22RjzgvCqaY6FcyeH-PQwqaeycHp6EZzMa84PCW7RiuNN-0Jl_DDIfy9mVLpRJenec4-U4XQy9Rqnyc7g?width=640&height=360&cropmode=none
describe: Manjaro + Windows

---

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script> <script type="text/x-mathjax-config"> MathJax.Hub.Config({ tex2jax: { skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'], inlineMath: [['$','$']] } }); </script>

# 618

由于笔者的笔记本（六代标压i5 + gtx965m）已经跟不上笔者剪辑4K视频和机器学习的需求了。所以在618的时候拿到了预算，有能力购入一台新电脑。



新电脑旨在追求长期性价比，并且尽量追求边际效应最小化。笔者是计算机科学的学生，因此未来可能会写CUDA，大型编译以及多系统的需求。适逢AMD的Zen2架构和英伟达图灵显卡大降价，因此618可以算是购入电脑的一个好时机。



# 配置

[**AMD 锐龙9 3900X 处理器**](https://item.jd.com/100006391096.html)  JD 3286.33

**[华硕（ASUS）PRO WS X570-ACE 主板](https://item.jd.com/100006445338.html)** JD 2890.00

[32G ECC UDIMM DDR4 3200内存](https://item.taobao.com/item.htm?id=601069952968) TAOBAO 1100.00

[Intel/英特尔P4500 1T SSD U.2](Intel/英特尔P4500 1T SSD U.2) TAOBAO 787.50

[**利民（Thermalright） FS140 霜灵 双塔散热器**](https://item.jd.com/100010498068.html) JD 228.00

**[美商海盗船 (USCORSAIR) 额定850W RM850x 全模组电脑电源](https://item.jd.com/6757737.html)** JD 818.17

**[索泰(ZOTAC)RTX2060super霹雳版OC HA](https://item.jd.com/100007391086.html)** JD 2491.00

[先马（SAMA）黑洞 黑色 中塔式机箱](https://item.jd.com/1842778.html)  JD 247.00

外加一根 U.2连接线 

外加问同学暂时借了块970， 以后会收一块RX580 给 Linux 用


**ALL 11848 ** 



## 购买理由

- 3900X 一共拥有 2 个CCD，总共十二核心，二十四线程。 每个CCD分表包含着6个核心和十二个线程 (id: 0-5, 12-17)(i: 6-11, 18-23) ，一共 64M 的三级缓存（虽然跨CCD时延迟会很大）。三级缓存对编译的提升是很大的，同时多核心更利于虚拟机运行。

- 华硕（ASUS）PRO WS X570-ACE 主板 是一块工作站主板，特点是支援ECC UDIMM内存， 主板用料较好， 有一个华硕自家的远程管理系统( 类 IPMI )， 共有三条pcie插槽(pcie 3.0 *16*+  pcie 3.0 * 8)。

- 因为<del>上科大水电网免费</del>学习需要，所以预计是24*7工作。ECC内存对于系统稳定性的提升是很大的。

- P4500, 英特尔企业级SSD，TLC颗粒， U.2接口

- 利民虽然各种参数上都不如猫扇（例如风压），但口碑较为不错，已经在学校内见到一个有组织的传教团队了（

- 因为会上双卡，所以上了850W的电源

- 目前，

  - 由于英伟达在CUDA领域较早的布局和在教育行业的疯狂送卡行为（一个电话连夜免费快递六块V100），AMD目前在机器学习和教育领域的生态已经落后n个身位了
  - 机器学习要求显存8G起步（测试结果来自 @ [murez](https://blog.murez.site/) 用openai跑历史的期末论文，注，后来他又自己写了一篇）
  - 同一个核心的丐版和顶级非公的性能差距不会超过5%
  - CPU更容易成为性能瓶颈
  - 2060S是20系列当前性价比较高的一张卡
  - 堆料索，售后走京东
  - 其它需求可以白嫖学校

  所以我决定采用索泰的丐版2060S

- 机箱随便买的



# 装机


装机过程将会在BILIBILI见面


# 双系统



因为学习需求，所以准备采用 host 系统上通过虚拟机来运行若干个 guest 系统。目前主流的方案有 proxmox, unraid, Hyper-V, kvm 等。为了更加开放和性能损失更小，所以选择了用  manjaro  作为 Host  ， 跑 kvm ，再用 pcie passthrough 将显卡直通给 guest 系统的 Windows 。



所以我将借来的GTX 970 插在了靠近CPU的pcie槽，作为Manjaro的显示输出。将2060S插在了第二根pcie槽，作为将来直通给WIndows的显卡。未来，将显示线插在970上就可以显示Manjaro，将显示线插在2060S就可以显示Windows。非常方便。



经过简单的配置，Manjaro到了可用的程度。



这里顺便吐槽一下学校的图书与信息中心，连交大的镜像源都会劫持。因此多了一步添加host。



![host](https://soygaq.sn.files.1drv.com/y4mrj9Rj0zOgz0EOIpkpnRyWadifw77lrYkxuQMCoJlSkJEEiLkIJx9MgDtVMuNPx8Xx8tlW4ikTXmvBBGkshjsRuRwU6xGGshcP3rCqHE6i9RL5BZ_W_x2r5IpLoZd8U3ssvWXhFskOPzXTE5jR4cLyE76h1Vk9awfc0MRuBe6JlwYppA6D02qJM6w3Zlm4ugI-gfoJGaYir-hwE4XCv_Bow?width=1306&height=770&cropmode=none)



## 检查CPU对虚拟化的支持

可以用该命令来查看CPU是否支持虚拟化

```bash
$ LC_ALL=C lscpu | grep Virtualization
```

```
Output: Virtualization:                  AMD-V
```

通常情况下的CPU都支持虚拟化。注意要在BIOS中开启。对于Intel处理器来说是VT-x，对于AMD处理器来说是AMD-V。

![AMD-V](https://soyzaq.sn.files.1drv.com/y4mERXPVkaLI_rxTvpcxmmO8pcx3pfK1V42NpBQ8YIizjA2rUH5eN5-ZvxJtG1YXuL2K63E1GALrQnWlEQPZ2f2Dmh-nh3PTQ4oZkeOoPl7bgVXuiVagOpHZVv97SIfJdKj8xVRicV-kzfDVe8tE0nSBJyl9RTlJRRezVwUMk04BhWY2zCq6CLKvGTWocjHb5-wkuWPIf82ze7ggnwcp1QMjQ?width=1017&height=70&cropmode=none)



## 检查内核

```bash
# 这里输出中需要包含 kvm kvm_intel
$ lsmod | grep kvm

# 这里输出中需要包含 virtio
$ lsmod | grep virtio

# 没有 virtio 时执行
$ sudo modprobe virtio
```



## 安装所需的软件包

```bash

$ sudo pacmna -Sy qemu libvirt ebtables dnsmasq bridge-utils openbsd-netcat
# 图形化的管理界面，如果使用带桌面环境的操作系统可选安装。我这里安装
$ sudo pacman -Sy virt-manager
```

- qemu QEMU 软件，提供 qemu-img 等命令
- libvirt 提供管理虚拟机、存储、网络的功能
- ebtables 桥接网络管理，用于 default NAT 网络
- dnsmasq DHCP DNS 服务，用于 default NAT 网络
- bridge-utils 桥接网络管理，用于桥接网络
- openbsd-netcat 用于通过 SSH 管理



##  启动服务

```bash
# 启动虚拟机管理后台服务开机自启
sudo systemctl enable libvirtd

# 启动虚拟机管理后台服务
sudo systemctl start libvirtd

```



## 配置显卡直通

参考: https://github.com/pavolelsig/passthrough_helper_manjaro

有视频，也有脚本，挺清晰的。

## 配置虚拟机

具体配置和 *配置显卡直通* 部分类似不过有几个点需要注意。

## virtio

Virtio是一种半虚拟化技术。用VIrtio虚拟的网卡和硬盘，其性能相比Nat和SATA有很大的提升。

[https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html](https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html)

首先将硬盘改成virtio总线以增加性能

![disk](https://qpygaq.sn.files.1drv.com/y4m3_hXpflJqT0pkajwotoQj6Qz5lEBcrcCWrcONnqUMB1lOnjJtArExo8zuBu2ku4jV5TXA3Xcz5bmYTB_SHTEcN76NRuP1E-_yNzHWh5xMY9GZQlmwf12EquK9LaWg8yX9f_LTKS1a-d4y5BDHqlZRInyYzAp_YveBVC_vgDYTM-n2numI83KSt-6pHp2FHpR8hfMnM-mAfvPE3ghhhcmiw?width=1354&height=1118&cropmode=none)

下载virtio驱动预备

下载地址页面：[https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html](https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html)

如：[https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso)

创建一个光驱，把驱动放进去

![virtio](https://qpyzaq.sn.files.1drv.com/y4mgdifFkECc5ueB-c39ZB9Vj9bA5KUWmHhAlbNdJhryqULNcGT3TGcrCbwBU9KBUEqUzrCzjH9jfShdhJ3SGV5wEgjglKGCIy1aduC9QtAYBg_mR2Y9OvT97GUxtUnI_DIotSYYZjjdN-XW8Wau-c6T9yRnkomtuFTHnGwlJ8ZNXdmnQzoEHW2uYeDjcXjxP3ZY76Hlp70qnoUio9FzEZsrQ?width=1393&height=1216&cropmode=none)

可以再做一个准备，把网卡也virtio化

![网卡](https://qpyaaq.sn.files.1drv.com/y4mlDYJ0pV5i40V-rSHks2bU0ZuqdRlpcezP9HIJnqwBXNfBvQoFGpbxRgQF3yARPfS7WF_OOqLV7vV2n0S7kAow-alkZEAVhkEPm7zZMT5qPPw21i1JMI4gdh1iwrWv3Aes2Y2tqx_5_7Ntini0NhS5PgZXJJEte178Mnkh6D8havpBDm8XB0x0izVWttp3Yn4gftyvfrXZ5BBmjlaOxC_zA?width=1448&height=1108&cropmode=none)

同时也可以把 usb host 作为 pcie 设备直通给 WIndows ，这样速度和便利性都比较好。但这样 Manjaro 上面就无法访问 usb 设备了。 如果你有多余的 pcie 槽， 可以考虑买一个 pcie 转 usb 的转接卡，某宝大概几十块的样子。



![pcie](https://qpybaq.sn.files.1drv.com/y4mdG-2uXMoZzVTFlKHFBKdKgH-Ny1mp4jVG2FSwfHGbUy4Vz2Z58pde2ByW51LlfCiSqk80YVASpGeoY4rYmlBLWnpHEHRg8kdxaaWpHw11dN51vRbljQ4pNPiqI74QqxNuRVyD5Y3CqdwVAEaT31Dv3U1pkrJhH4Ugu5s9nmaNLL7e_hKiQPFLuVJh7qpCC-pO752rWjAmqIetTMoHDXGkg?width=1909&height=1184&cropmode=none)



在 windows 安装时，需要加载硬盘驱动，驱动文件夹地址是 `E:\viostor\w10\amd64` 

当开机成功后，右键计算机 - 管理 - 设备管理器 ， 然后对于未知的 pcie 设备选择安装驱动。 将驱动文件夹指向 你的光盘即可。 目前我一共装了三个驱动。 分别是硬盘，网卡还有一个virtio ballon，用于CPU的调配。

![win](https://qpycaq.sn.files.1drv.com/y4mQCc-SSCUuO-wJ3MZpJunX3bt_2UqQ2j_VDKysFNtTeMUENmsLOIYaKv0wnuUS6lHsQcezjlB_eUiziJCOBOcpsyIpi-UbKSyFfmG0DA2ig6B5DT6YP4wLttKpxjYKEd6UufnlChg7iENsoh-LxpLNdBJj5lIphZtZrwGkW0RkjOvEFEJMUphtNTFGJ1QHd49NaJjB0TrmAzR1zmEFfeoig?width=1280&height=819&cropmode=none)

## 显卡

注意开了显卡以后就不用再开虚拟显卡和虚拟显示器了

英伟达显卡的设置稍有不同。

要让显卡直通，先要启用iommu，然后让pcie设备使用修改的rom。启动iommu在主板里就可以设置，关键是昨天的那个rom file那一行xml。

![rom](https://soyaaq.sn.files.1drv.com/y4m9IVjh_nr30EjIjHc_AgdD6kHqmQ4szPoMm9mmoopNgjAsbgTRQ_n9EVRdQjEKdY9oqM3j4UhCTC7zVMMk7ZcLGotRqeljOyOvPTAW5CNaAbtxY4IFaTqWvvxCJlzcx-AiQnyZXL3BGrCUWvLFnSj9OscfXKWbbvndnxOC4gRci4yop5Kf_JH2pR5r5IiqIjTI2syPkCVyjVZRF7ismgGrg?width=1922&height=1310&cropmode=none)

这是一个显卡的修改bios。首先要获得显卡的原生Bios。

最简单的办法是在Windows上的GPU-Z上导出Bios

![GPUZ](https://soybaq.sn.files.1drv.com/y4mZk-xCzuHAwpTVd08CCbp9znoZH08UsFrksRcgwJfYuAoUJga0-WSCcuyrpYfzIHMvJjJ9C0LP0vYWGQZdi-I1C5VIP2Mr6x2n_89MnVUrM9t0u4DS7RjMAnNn5lM0Vfgnygef3pKX4kMPL_MGbDzDApyBLtNHKM7bSyBn6SH3ulT82Vi11DawuaVSUi3Pbnu-K8qHOupm90ybNKMod7IfQ?width=1280&height=335&cropmode=none)

如果已经装好了 Manjaro，也可以在这个网站找到显卡BIOS

https://www.techpowerup.com/vgabios/

然后就是修改ROM了。首先用 WINHEX 或者 HXD 来打开十六进制文件。全文搜索  VGA 。删除 55 AA 的全部内容。前面基本都是 nvflash 啥的信息，这些可能会导致显卡不支持 efi 。

![](https://soycaq.sn.files.1drv.com/y4mZHzeHTw5XRXZpDnGXB0hPiGaQ-n0ztcqxHgr6p4sB8n5TqdbmzeAegrBcz6NFJ0M6IfcJUmXjscSyJvwH-99So5wuih8H9YPP2_3YnfXekAsrgtj6lYKPLz6CM2Jsn9t5gju4ijV4HuT0yb1945jd0dGXV9XRyHDRIESQlQJVUODkkRtVetPsdSJIABQeV31MeDCU1V_bcC3lGkWs77hnQ?width=936&height=582&cropmode=none)

**最后的文件应该是55 AA顶格的**

​	![结果](https://soylaq.sn.files.1drv.com/y4mqaWim6oG4HewSk7h-1MOy6GxHfPL4eHX28fxPRmgbCELv7xZ7fb-5aGC2_31AUDj-6pIbhdRavtGP2mq5aFkwAqbzfnM50O1y5bQ07kttLlANTTckXzZBiSmVyTo4YgqcWu8ypDpgG20VbKskDPpjPhWDujsMWARqSXzY6BMM4m6rrMmJLNg2zKxz5VbG3Kn6B2NWtJAGgmYhhMg7uPW1Q?width=901&height=586&cropmode=none)

然后保存为 .rom 文件， 复制到 Manjaro 里面。在虚拟系统管理系统中的xml文件中加一行<rom file = >用于加载显卡bios

当然事情没有那么简单，NVIDIA屏蔽了消费级显卡的驱动在虚拟机上的加载 (黄氏刀法.JPG) 所以我们需要编辑 kvm 的配置文件来骗过老黄。

[https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF](https://wiki.archlinux.org/index.php/PCI_passthrough_via_OVMF)

```
$ sudo EDITOR=nano virsh edit win10
```

win10替换为你的虚拟机的“域”名字

![win10.xml](https://soymaq.sn.files.1drv.com/y4maolZ5cTRWpuof5nWjhUStCbbkPZYoI_n8Pqf56upTKzY-wlYRB2xgWn0VCko78pOfXrX5lbQwNWWvhcRIRcGXDB4tq--DEVpMSr041kzbgXt3x8Esfw7vFLdoUeghvCgDbJNukTy2Bhc2ViCGMBXtF1ApbqYcfnjcFfr3ENq8_fmYDUDO0xnvuzQy_5nRogaWa3TUk1aZqF1ED4MYcs2Og?width=2032&height=1200&cropmode=none)

添加一个 12 位的 字母数字混合的 vender_id 然后开启 kvm hide 

之后就可以当正常显卡使用了，安装驱动后跑 CUDA 和 3D 项目没有发现明显的问题。



## CPU

之前碰到了一个问题是虚拟机可以检测到12个逻辑核心，但从任务管理器中只有两个逻辑核心被用上了。后面发现是CPU的配置问题。

这是我的设置。因为 3900X 是胶水总线的设计，跨 CCD 的延迟较高。所以我将在一个CCD上的物理核心对应的物理核心分配给虚拟机（也就是0-5 12-17的逻辑核心，3900X 瞬间变成 3600X）。当然你也可以将12核24线程全部分配给虚拟机，这样的话cinebench 可以跑到 7300分 （我做过超频，全核4.3GHZ， 在原生windows上可以跑到7700），但某些应用会出现无响应和推出的情况。所以我暂时还是选择分配一般的核心的方案。

![CPU](https://qpydaq.sn.files.1drv.com/y4mdD81pR6OfRe1eqNfr3EWDkboUSWEwl16F0hjzapTCUxC1vUKukYg91Bmn8vhf7QJT-_0Pz2V6GUbiVyITbnOPc7HKaWqt2QOKG7orO91Nu5RPNsW40POvSor7ISuWzTjyzEGfUiniFnZr26o_GsS6VoENVRA0c9mp_oNAQRarSCuda3gPEW1B6XUOo7HyRz5dHSu17VCXxH7RqF-6qvTsg?width=1425&height=1199&cropmode=none)

![](https://qpyeaq.sn.files.1drv.com/y4mHmMDOVSBk8yKYx25zcMFGyWXooWM39oLzLBydpg2VejI6Ls6b8Eecj5NldnVR_QkRofsDqrOitLMdQQELUYKp8qx5hfk0UjU70VE9wQwKJHZqoi_Clnt7MmxzIIF30mDBV-2g0AVMvugd0g4uPnOscG8zPksye2SLDXiWK2KNJ3YcXaJbngSHdHg3Gkh6EhPbtoDRfd2sHflpayH3_wwxQ?width=1538&height=1230&cropmode=none)



![CPU](https://qpyfaq.sn.files.1drv.com/y4mvaCQiu9OjjUdaUw8_FhOgCmA5o2BYc0LSf59Oh-dz0akOgo3aqq2MNpTUBz0Ak8U0lfM5rKYTXWJEOBS2uoxiIsRqNa-Orvfcn25Eu7LxqSiufThws1sld-mGPz_A7Pbsng1GkKv8VWysVsui4KMN8lujM86pJogM2JWncdFeJoag2rvnc3PxGaKoy_-I80z-JfmXnlH9Be0XkdHJoEAzg?width=1445&height=1214&cropmode=none)



# 参考与致谢

- [KVM (简体中文)]([https://wiki.archlinux.org/index.php/KVM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)](https://wiki.archlinux.org/index.php/KVM_(简体中文)))	

- [在 Arch Linux (Manjaro) 中使用 KVM 虚拟机]([https://www.liuzhixiang.com/2019/05/18/Using-KVM-on-Arch%20(Manjaro)/](https://www.liuzhixiang.com/2019/05/18/Using-KVM-on-Arch (Manjaro)/))

- [[pavolelsig](https://github.com/pavolelsig)/**[passthrough_helper_manjaro](https://github.com/pavolelsig/passthrough_helper_manjaro)**](https://github.com/pavolelsig/passthrough_helper_manjaro)

- [Luv Letter - dual-system-osx-kvm-tutorial ](https://luvletter.moe/dual-system-osx-kvm-tutorial/)  也是 Luv Letter 大佬给出的计算机配置

- 感谢 [Kiwi](https://nyan.kiwi.cat/) 提供的帮助