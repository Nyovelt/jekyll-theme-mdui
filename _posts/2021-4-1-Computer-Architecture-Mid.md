---
layout: post
title: "CA1-MidTerm 梳理"
date: 2021-4-1  10:02:59 +0800
category: EDU
tags: EDU
img:  https://oss.aaaab3n.moe/uPic/DDvSSC.png
describe: Computer Architecture
---



## Intro

### CA 六大思想

1.Abstraction(Layers of Representation/Interpretation)

2.Moore’s Law (Designing through trends)

3.Principle of Locality (Memory Hierarchy)

4.Parallelism

5.Performance Measurement & Improvement

6.Dependability via Redundancy

- 其实原版的 **CS61C**在 21 spring 改成了五大思想(逃)



## Numbers

### 整数

- Everything in a computer is a number, in fact only 0 and 1.
- Integers are interpreted by adhering to fixed length
- Negative numbers are represented with Two’s complement
  - Overflows can be detected utilizing the carry bit

- 整数分为 unsigned 和 signed, 对应原码和补码
  - 关于补码的运算和溢出:
    - ![Nr50nu](https://oss.aaaab3n.moe/uPic/Nr50nu.jpg)
    - Overflow ERROR: When the result of addition exceeds the range of $[-2^{n-1},2^{n-1}-1]$
    - Overflow occurs if and only if two numbers with **the same sign** are added and the result has the opposite sign.
    - Generally, 负数的二进制值要大于非负数(因为首位二进制为 1)
    - `Unsigned` 对应 $2^n-1$
    - A **neat trick** for flipping the sign of a two’s complement number:  flip all thebits and add 1.
  - 2 problems (in 6-bit binary):
    - ![N9vNwe](https://oss.aaaab3n.moe/uPic/N9vNwe.jpg)
- The decoding and encoding system are human-defined, so that it can represent any number as wished. That is, no largest interger represent by n-bit numbers
- $\bar{x} + x + 1 = 0 $ because of `overflow`
- **n bit represents $2^n$ numbers in binary**
- 2 进制适合计算机, 10进制适合数手指, 16进制表达更方便

- Consider `>>` operations, for signed int,  >> 0 get signed bit; for neg get -1 (-1 >> 1 = -1)

- ref:

1. [About MSB and Overflow:	](https://stackoverflow.com/questions/29330787/signed-overflow-why-carry-in-and-carry-out-of-msb-should-match)

### IEEE 754 - 计算机数字标准表示

- Bias: 偏移, 即在结果加上偏移指, 或者 0b0 代表了偏移值本身. e.g. 0 -> -127,  0xFF = -127 + 0xFF = -127 + 255 = 128



- ref
  1. [IEEE 754 Simulator](https://www.h-schmidt.net/FloatConverter/IEEE754.html)
  2. 

