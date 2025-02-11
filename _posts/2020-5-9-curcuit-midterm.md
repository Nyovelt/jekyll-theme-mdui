---
layout: post
title: "电路基础期中概念梳理"
date: 2020-5-9 9:00:59 +0800
category: Education
tags: Education
img: https://5c8qzw.sn.files.1drv.com/y4mBIHNi_Z3ekXuvWz0wyQGrPj3WU2DN_IH-wc965zLTbW5SGrjzvzgq10BdT3lv2nPYPRa5pMZcwDj2lRTpPN8bdFveYN5j1VPB58hr8ikSab7RQgjWgfax5-n-axGhkqATx-cQyFtw7fcGzgy_uR7e50MtW657S3I5VPQ9bXF8iqAOrUBxGiW0g7T-oc9pailfqA9cZDwecgc_ZYu3-cKiw?width=615&height=285&cropmode=none
describe: 电路基础期中概念梳理
---



<head>
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script> <script type="text/x-mathjax-config"> MathJax.Hub.Config({ tex2jax: { skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'], inlineMath: [['$','$']] } }); </script>
</head>

# 电路基础

## 电路基础基础

- $i = \frac{dq}{dt}$
- $v=\frac{dE}{dq}$  , E 指电荷移动做功（功有正负之分，取决于电路中电荷移动的方向）
- $p = \frac{dE}{dt}$   单位时间内做功的能力
- $v = L \frac{di}{dt}$  L为电感，因为当$\frac{di}{dt}$不能为值无穷,所以含有电感的支路$i$不能突变v
- $i = C \frac{dv}{dt}$  C为电容，因为当$\frac{dv}{dt}$不能为值无穷,所以含有电感的支路$v$不能突变

### 理想电源

#### 非受控源

恒定的输出电压和电流

### 受控源

根据电源两端的电压或者通过电源的电流，以线性函数的形式，产生响应的电压和电流。

## 基尔霍夫定律

基尔霍夫定律分为基尔霍夫电流定律和基尔霍夫电压定律。它的本质思想是电荷不会凭空的产生和消失（电荷守恒公理），所以电流——单位时间内电荷的数量，以及电压——能使电荷移动的能力。这两者是守恒的。对于每一个形状闭合的电路，都可以有:

- 电势涨落守恒
- 电流的数量和为0 (电流为有向标量)

于是我们有方程

- $$\sum{v_i} = 0$$, $v_i$为每个电路元件所吸收/输出的电压

- $$\sum{i_j} = 0$$, $i_j$为电路上选定的任意一个点，它的每个方向上的电流

### 分析方法

- mesh analyse
- node analyse

## Superposition

因为电荷的正方向移动和负方向移动可以抵消，所以电路是线性的，可叠加的。因此产生了电路分析方法——Superposition。

0. 让电压源短路，让电流源断路
1. 分别计算每一个电源在某位置产生的电流/电压
2. 对这些电流/电压做代数和

即，每个电源单独作用的和

## 戴维南定理

如同黑箱一般将任何线性电路等效成一个电压源 + 一个电阻

0. 观察法求等效电阻
1. 求开路电压和短路电流
2. 外加电源激励
   - 适用于受控源
3. 诺顿定理同理

用处

- 求最大功率输出
- 简化电路

## 运算放大器

$$ v_{output} = A(v_1-v_2)$$

- 负反馈

### 理想运放

- $v_1=v_2$
- $i_1 = i_2 = 0$
- useful in circuit analyze

### 类型

0. 以下所有都可根据基尔霍夫定律轻易得到

- 反向比例放大器

- 同向比例放大器

- 电压跟随器

- 加法器

- 减法器

- 共模抑制

- 串联

## 一阶电路

- 电阻，电感，以及它们的公式和储能和串联和并联
- 依旧由基尔霍夫定律推导
- 阶跃响应与自然相应
- 三要素法

## 二阶电路

和一阶电路类似，更加考验微分方程。（完）

  

## 易错点

- 电流由高电势向低电势流
- 在KVL中，受控电流源可设为v
- 读电路图不需要视力
- 能用计算器就不要相信你的手
- 



