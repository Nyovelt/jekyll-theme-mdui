---
layout: post
title: "Intel Hackathon 2020 记录"
date: 2020-12-17   10:01:59 +0800
category: Life
tags: Hackathon 
img: https://snz04pap002files.storage.live.com/y4mc6Aov2p-1R6vyEgbXLImii73JwqlZo0kxTQPHnN4xWmXuFHnjNlJ2sdAXRTJU8f7T4FA2HkN1KJm-vJRB_pjBmyyB-OJnIv8s1YchRRxUpqZIANBoZtN6QrIUF_ZgmjnDJ0EA3xnp4UeSdivAQpFceuHEBKyxRFsxdFaZD-w6iuxD4Eh9R3X-1g8ACuGndN2?width=1024&height=768&cropmode=none
describe:  这里竟然是大鸟转转转酒吧

---

# D-14

大约在两周前收到了来自社长的[推送](https://growthx.mysxl.cn/blog/2020-intel-open-innovation-hackathon)，正好最近想恰点钱，之前的外包也没写完，于是准备去参加。正好 DEEMOS 实验室在做 CV 相关，所以我们自然选了赛道二。

![](https://snz04pap002files.storage.live.com/y4mtcABB9kMXECRJ_GAdeJZK_1CVJSkz3GHQ_swor95mB9ylzSIfpd-dkzCOJgzzAVxobUKY3DbPfDcSE3DvV-ThXLPCUvKolzJdBwD-h3ePzC4Yikt_5ESrpIdQmzqDtyGc--p2dRoP5wfzdArdeXg2RMtiICu3k2FzouF3IhRQWBQ5icNXpUD1X6VliEOb6cz?width=1080&height=708&cropmode=none)

一开始的时候 intel 没有释放任何消息，不过我们猜测他们会给一个 SDK， 可能是超分辨率或者怎样的。但两周后就要参赛了，所以我们只能先找一个不错的创意。我们想到了将 UGC 用于匿名聊天，因为 DEEMOS 最近在做[人脸风格化](https://anome.geekpie.club)，而匿名聊天如果能让用户定义自己的形象，而这个形象和本人又有关联，不仅有趣还能规避一些问题。

社长这时候说，他们以前去 Hackathon 通常都是在之前做掉 75% 后再去现场的，所以让我们提前做好充足的准备。

我们进行了更加深入的讨论，我们大胆假设了 intel 给的这个 SDK 性能可以做到实时的视频处理 <del>因为连这都做不到这个网络也太菜了</del> ，所以我们考虑了诸如狼人杀，剧本杀，跑团，语C，桌游模拟器这类。因为我们认为在游戏中加入风格化后可以让游戏变得更加好玩，而且像剧本杀这种本来在线下就要化妆，而且很好玩也很贵。如果在疫情这种时候搬到线上，如果我们做的效果还不错的话，那应该挺吸引人。intel 在本次比赛中还需要商业计划书，于是我们制定了 ToB 和 ToC 的两套方案，既可以卖给企业又可以做一个 app 给大家玩。

最后简单分了一下锅以后就散会了，一切等 intel 公布 SDK。

# D-7

在 D-7 日的时候，intel 公布了他们的 SDK，是一个将视频中的某个长期存在的对象进行替换，比如将猫片中的猫换一种眼睛的颜色这种。除此之外没有任何其他信息。如果我们要继续做之前的方案的话，这个 SDK 能不能进行直播我们是不知道的。

于是我们又讨论出了一套方案，主要市场在于特效拍摄。灵感主要来自于影视飓风的一期视频。主要是人物，风格，场景的改变。

当晚我们下楼去 DEEMOS 和学长们讨论，<del>被批判了一番</del>。 UGC 和相关技术的论文在13年就有发布，但是一直找不到合适的落地场景。我们提出的"Fantastic Movie for everyone"明显不显示，否则早就有人做了。而且我们想的 styleGAN + intel SDK 几乎人人都会想到。而剧本杀的市场太小了。同时这项技术没有技术壁垒，快手抖音很方便就可以占领（虽然他们目前的滤镜效果一塌糊涂），学长传授了他的一些人生经验：多读论文，多看新闻，多上知乎。另一位学长提出了是不是可以往电商直播方面想，因为目前直播的互动其实不多。

于是我们回到了社团，并且开始了`多读论文，多看新闻，多上知乎`。发现学长不愧身经百战，因为我们找到了 intel SDK 的 source ...

> https://github.com/OndrejTexler/Few-Shot-Patch-Based-Training

居然连代码也不改，放在 ShanghaiTech 就等着收查重邮件吧

于是我们对代码进行了评估，发现可以做直播，于是又继续讨论。社长希望我们发挥年轻人的优势，发现其他人发现不了的热点。

# D-4

我们讨论了几乎所有东西，比如虚拟偶像，电视剧换装，婚恋市场，早教市场，艺术市场等等。发现还是做制作视频时的风格改变比较好。至少有60%的希望成功吧，如果 demo 做的还不错的话。于是开始了分工。一共五个人，一个人准备slides, presentation 和商业计划书； murez 负责神经网络训练; Logan 负责辅助神经网络训练；Leomond 负责后端和直播推流； 我负责<del>打杂</del>前端和服务器。

与此同时，我们准备复现一个 demo。

https://www.bilibili.com/video/BV14a4y1W7ua



# D-2

晚上的时候， Leomond 骑平衡车刷运动世界校园的时候由于上海沉降严重路面凹凸不平导致多处皮外伤，他<del>坚持跑完了运动世界</del>后来社团寻求帮助。

![](https://snz04pap002files.storage.live.com/y4mBVc33ZQPRxLrMEmLWt_9ONBMbz2979OdMKNDNmd57prp_uSGCTY4k8DV5HrtiWYAEgBr3pqGCTAIHVbS6fYl6Qz4KFtVM45wiH6HW5LAtlnqJTaVsxq0DXGebe1Kj01GCb2Y1fve0wNMYFZ6eGk05DSwrbvRDofOJVvra__9_jObxF4oNgIu-ezwLe_rwwcb?width=768&height=1024&cropmode=none)

好在 ShanghaiTech 每个实验室都有 First Aid，几位大佬对急救的处理非常得当， Leomund 在学校也有常备药箱，附近实验室最近买了小米的平衡车轮椅套件，最终在凌晨3点， Leomund 回到了寝室。当晚， murez 开始训练第一个风格。

![](https://snz04pap002files.storage.live.com/y4mDXcwzsnj1ZAQprxA3MNFf5t13Zqld77kcixHPTUfktm2a9f-Jcd2Po00WHE4ZMwJQh4Jk9x9fOA2Hz80flOi70ED25Boo5jc9N-vB86e0O7TJxlzP2t8aRkFo5EnPljPtyJ9T3rNIoFMv-J_3VRAsiP42tWOS2tvI1plGxqYyZy87sk3RSWDP5QkkbdbF0A-?width=1024&height=384&cropmode=none)



赛博朋克2077 于今日8点解禁。



# D-1

中午打包出发去北京。路上我的 surface 还因为磕了一下导致触屏报废 （开始收集 mbp 预算）

总结一下，我们这次在去北京前完成了

- 整个计划，包括商业计划
- 前端模板，还缺和后端对接部分与推流
- 多个 styleGAN 模型，一些是实验室有的，一些是刚刚训练的，赛博朋克2077的模型也在当天开始训练
- etc

同时我们全部开发人员配置了连回社团的 wireguard， 并用 FRP 映射出了三台服务器。因为我们有HPC团队的成员和实验室的搬砖工。这些机器包括

- 来自 HPC 的 2*64 核 AMD 处理器 + 8 * NVIDIA V100
- 2080Ti * n

光说这些资源也没什么队伍能打败我们了吧（逃

乘上了3点的高铁，高铁上的网络信号还是一点都不好，导致我们睡了一路。当晚入住了中关村旁边的如家酒店，黑客松还带酒店的体验是非常好的。当晚吃了旁边的达美乐。出来以后看到酒店门口一群人在玩滑板，想到如果有一段这样的风格化视频也是很好的，于是在他们的同意之下拍了一段他们的视频。

当晚继续之前的计划，应该是凌晨睡觉的。murez 训练出了赛博朋克风格。

![](https://snz04pap002files.storage.live.com/y4m_bkzZiyEh52EZm7qkEFnBtwBU5ctKfRPK63Mb720n29fneduoyrxnQvNmeklnhDPesm74fOxWlRbLSDO0TF4_xWzsZvPG01JrP0jrYGtiJmZKlcELA1chbjo1I62OEVlIXhG0v0XJp8SRpxbycpG5yun0WSfV3PJCZsp2wqEh13adM0X6RGdmqVkqIHoeC2T?width=1024&height=346&cropmode=none)

# D day

早上8点打车来到比赛现场，现场的早饭是星巴克的可颂，TUNA和培根，不限量供应。（所以一个队友一个早上吃了五个，值回成本）

进了会场内发现周围挺空的，就我们五个人占了一桌，铺了六台电脑显得有点挤。隔壁一个上大的研究生只身前来，还没做什么准备，看到我们的架势被吓了一跳。

中饭和晚饭都是自助餐，就是有些辣。

晚上出门的时候还下雪了，照了一张 Google 的雪景。

![](https://snz04pap002files.storage.live.com/y4mqxf5lMBeXrBXOmwqfGFmh8eiFvJe5-OSYyUlW8O-PnIhQh2tdWYRsko1lPj0S5Cxd67FUtHknEh73NtWyuNV00H4I3eQTQlll6RybBauGA7Qn5ySU6kfgdV3b2srX07rIP9wv1PaqVR4OfnwFg66ffEi0eLr9-my9IfhtGGDVsLY5yh73rSlv3_63LIjP6tI?width=1024&height=803&cropmode=none)

当晚回酒店后又是很紧张的调试。 Logan 帮我解决了 Vue 的一些问题，凌晨的时候我把和后端对接的代码大致写完了。负责 pre 的队友和我们商讨 slides 和措辞，并且由同学帮助美化 slides。L 努力的优化 ffmepg 的推流，通过添加 buffer 的方法将直播帧率提高到了 512*512@17fps 。这个帧率是将源视频推流到上科大校内的某台服务器，经过处理塞进神经网络再推流回来的，所以这个成绩非常惊人了。 



<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/TXjWvf2tOGk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



而 murez 正在努力的进行赛博朋克风格的训练。我凌晨2点左右睡觉的，后来我才知道，murez 坚持到了凌晨 5点。



# D+1

这一天主要是开发的收尾工作。因为前后端对接问题我背了一些锅。其中发现了 Flask 的跨域问题，因为之前没有人用过 Flask 所以踩坑时间有点旧。不过最后因为 pre 效果的关系而没有采用前端演示的方案。



商业计划



最终我们的效果是[这样的](https://onedrive.live.com/embed?cid=82ADEC2E6379EBED&resid=82ADEC2E6379EBED%2167356&authkey=AA9LqBh8H-KqtZ0) ，并且在5分钟的pre里面演示了现场直播，效果还不错。其他组也有做赛博朋克风格的，但是完成度没我们高。上大的那位最后做了教育方向，希望用这个神经网络来教别人艺术。我觉得想法挺不错，demo也还可以，但是 Q&A 的时候感觉完全没答到点子上...其中还有一个公司做的是虚拟偶像。一位不太年轻的大叔在会场上公然放爱酱的视频，想做一个24小时的人工智能驱动虚拟偶像。他最后说了一句，”虚拟的偶像，不虚拟的经济“。看样子这些人还是 too old too naive 了，我觉得大部分都是泡沫（难道是我老了吗）。最后他们是第一名。



Intel hackthon 一共有四个赛道。还有一个高二的学生一个人一组，用毫米波雷达做耳机的手势控制。其实我们觉得在耳机上加毫米波是不错的想法，如果用毫米波去扫描耳道建模就更好了，他拿了三等奖。



晚上的时候和姚班的东西在宇宙中心五道口约饭。比赛房赞助的酒店到期了，我们在airbnb上找了一家民宿。说是五个人，这家民宿只有三个大床房。**男同竟在我身边**



![](https://snz04pap002files.storage.live.com/y4mdjGn7VkV2YRmz_7d1xSQY4kIETTLHNxoSZMCEEt1Nwc73WvU_9cbIYQCJiV0M9gzh1mwNXsmVKuTU2_ysC2oJH1ZbeJac-K5J-XRvMAFWRGTArRGsJ-iR9DLKYHM5jIQC6yd_trSCW85akQmisHW-I6o7o5PHThaYhv4pk3QxVMIcZRLIGGcsfgagboIVF_M?width=720&height=270&cropmode=none)

# D+2

简单游览了一下就打道回府了。我去北理见了同学，吃了食堂的北京烤鸭。队友说要买烤鸭感谢社长，于是在嘎韭菜一条街的第一家店买了烤鸭。窗上写着64元两只。当我们走到中心的时候，见到了无数的稻香村，而且已经变成48元两只了。