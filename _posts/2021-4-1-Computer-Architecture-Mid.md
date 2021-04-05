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

- Bias: 偏移, 即在结果加上偏移指, 或者 0b0 代表了偏移值本身. e.g. 0 -> -127,  0xFF = -127 + 0xFF = -127 + 255 = 128 (IEEE 754)
- 第一位为符号位S。 接下来八位为指数位E，剩下23位为分数位F (32 位)
  - $ (-1)^s \times (1+F) \times 2^E $
-  大小比较：
  - 首先按照指数排序， 再按照尾数排序
- 64位系统中: 1 位符号位， 11位指数位， 52位小数位 (double)
- 当指数位
- Overflow: 
  - 在极大值的情况下会在指数位上溢出
  - 在极小值的情况下会在指数位下溢出
- 特殊情况： 
  - 指数位全0： 极小数
  - 指数位全1, 小数位全0： Infinity 符号根据符号位确定
  - 全0: 0 , 符号位不确定
  - $ \infin - \infin , 0-0 $:  可正可负，指数位全1， **小数位不确定** => NaN
  - ![](https://snz04pap002files.storage.live.com/y4m6TNxePdpVx9OzxurZtXVgs0-3fOLclDH0k7i6GFR3uBJmyk8ExJerz1sM9rEUi2gDGXBNU2NHWYYn7cmpsI04cntU594NFCKadeg1W7veTAX8Nrp8zBbop67NuExwcynOXvvd7VlJ-B1zwI3YedANrzaQK6GW0eWPzGDp22_UNnaiwo4neyy6yF2lyUlivRI?width=1074&height=354&cropmode=none)
  - ![](https://snz04pap002files.storage.live.com/y4m8LUJ7roIO6DfM8Bg74PzkjMI4rDQkW8wQUEOG86Ic1f9Zp3McDQKf7S3H_CwDY-h4dAeEJmNOOTr0QSF8xyLlBBCpB2eq0aawyEuDJ9ELdyJRghZMKfiISI-ozXtr9kkbT1D0rbjuCvGLv5-4ywitiilZbjrblsxFmp3dGvRdm6q2TqhQnoEJZX26KKcVHBk?width=1468&height=400&cropmode=none)
- 浮点数不精确
- NaN 不可比较 (仅仅在 NaN $\neq$ x 时成立)
- Smallest Gap: $2^{-149} = 2^{-23}*2^{-126}$
- 运算无交换律
- 最大整数: 0b0 11111110 全1， 因为指数位全1的话会变成 inf
- 计算方法: e.g. 53.125 =? 110101.001 => $1.10101001 * 2^5$ => 127 + 5 = 132 = 10000100 => 0 10000100 10101001 000000..
- 加减法： [https://www.youtube.com/watch?v=wC950FKNl8Y](https://www.youtube.com/watch?v=wC950FKNl8Y)
- ref
  1. [IEEE 754 Simulator](https://www.h-schmidt.net/FloatConverter/IEEE754.html)
  2. 

## C Simple

### Pointers

- Dangling references
- memory leaks
- C 是一个弱类型语言， 也就是说对于每一个类型，结构， 对于其边界没有严格的检查。对于指针也是这样， C将自由和信任交到了程序员手上， 因此会产生很多不安全的内存访问行为。
  - 而 Rua st 则添加了诸多限制。
- 函数指针：
  - char *foo(args)
  - f = &foo 
    - 能被取地址
  - (*f)("cat", 3)
    - 能被调用， 返回 char*
- Tricky:
  - int* a[10]; => a is an array of pointers
  - int *a, b; => a is pointer, b is integer

### Memory

- 程序一共有四块内存区
  - 栈：存放局部变量
    - return address, arguments, local variables
    - Constants
    - see [编译时你在做什么？有没有空？可以来拯救吗？](https://aaaab3n.moe/technology/2019/11/29/c-cpp-black-magic.html)
  - 堆：存放动态变量 - difficult to manage
    - malloc
    - calloc
    - free
    - realloc
    - 
  - 静态：存放 functions 之外的变量
    - Static variables
    - Global variables
    - Constants
    - String Literals
  - 代码：当程序运行时存储，不可更改
    - Machine Instructions
    - Constants
  - ![](https://snz04pap002files.storage.live.com/y4ms7978SdKAnRisT2pTi3Dt7K-57msUyxYmihpxd3Op6N3vVU322DnJGUOYX8xrL9gEoZe0CeXa8JK4O1wp3TbenlHKWoIBk1EW_AukWnx-hDhpUcdGxPZ5-mmaqwBGhpx1lV4dsgRKmhXNSuoMKAqnOphlOLlK4T5Yy-Q194wF6J9Nt284s9mLD1bfdaMg6Ek?width=784&height=858&cropmode=none)

- string:

  - ```a = malloc(sizeof((char))* strlen(b)+1)```
    - pay attention to **+1s**
  - `strcpy(a,b)` 不安全， 因为不知道结尾(\0)
  - `strncpy(a,b, strlen(b)+1)`安全。  if its too short it will not copy the null terminator!

- Union

  - hold different types in **the same location**
  
  - 也就是说会存在**内存复写**

  - size为最大数据结构的大写， 对齐到 4k ， 例如 char 虽然是 1 字节， 但 union 会 size = 4

  - [https://www.geeksforgeeks.org/c-language-2-gq/structure-union-gq/](https://www.geeksforgeeks.org/c-language-2-gq/structure-union-gq/) Q8 is worth doing Q19 helps understanding
  
  - ```C
    #include <stdio.h>
    #include <string.h>
    union a{
        char b[6];
        char c[9];
    } data;
    
    int main(){
        // char d[7];
        union a A = {"abcdef"};
        strcpy(A.c, "12345678");
        printf("%s", A.b);
    }
    
  output is 12345678
    ```
  
  - ```C
    #include <stdio.h>
    #include <string.h>
    typedef union {
        unsigned long long num;
        struct {
            unsigned short a;
            unsigned short b;
            unsigned short c;
            unsigned short d;
        } data;
    } Datatype;
    
    int main(){
    Datatype y;
    y.num= 0xABCDDCBADCABAAAA;
    printf("%d\n", y.data.a);
    printf("%d\n", y.data.b);
    printf("%d\n", y.data.c);
    printf("%d\n", y.data.d);
    }
    
    // result is proved by gcc, clang, msvc on x86 devices
    [Running]  gcc test.c -o test && test
    43690
    56491
    56506
    43981
    [Done] exited with code=0 in 1.206 seconds
    
    >>> 0xABCD
    43981
    >>> 0xDCBA
    56506
    >>> 0xDCAB
    56491
    >>> 0xAAAA
    43690
    >>>
    
    ```
  
    - 由此可见， 结构体中的类型是自上而下的，而小端序是先下后上的。于是产生了 x 反而对应末尾的2字节。
  
  - ```C
    #include <stdio.h>
    #include <string.h>
    typedef union {
        char d[4];
        int e;
        struct {
            char a;
            char b;
            char c;
        } data;
    } Datatype;
    
    int main(){
    Datatype y;
    strcpy(y.d, "ABC");
    printf("%d\n", y.e);
    printf("%c\n", y.data.a);
    printf("%c\n", y.data.b);
    printf("%c\n", y.data.c);
    }
    
    Result:
        4407873 //0x434241
        A			
        B
        C
    ```
  
    - char[] 类型或者数组类型也是自上而下的， 因此在内存中从上往下是 'A', 'B', 'C'。 int 按照小端序读是从下往上读，从左往右的拼接数字，所以是 '\0''C''B''A' = 0x00434241 = 4407873(D)
  
  - 如果感觉自己被加强了，可以试试 [3(b)](https://robotics.shanghaitech.edu.cn/courses/ca/20s/notes/midterm_sol.pdf)

## RISC-V

### Instructions

- ![](https://snz04pap002files.storage.live.com/y4mwCiM1SDGRlQ1V0upPdLAAdT0SGq8eQbvbgdNV7RogPurxVjCRvqXCPaoUTJretOunNk8Ty6PlCZR18DdfV_1mi7woJi0WkqK0XVqcwZdCstrBc0UowRJ9Xwo2IMF8vMph-gaUcEXm3B8k1w0VcSYN6AYnVXcLhAXLMBdWy_qzpdM6qs7xJdkxzNk9Tut7SLz?width=1816&height=534&cropmode=none)

- Techniques:
  - *=-1: 取反加一
  - 寄存器置零： xor zero
- 函数调用
  - a0 - a7: arguments
  - a0, a1: return value
  - sp: 栈指针
  - when jal to functions, a0-a7, t0-t6, ra need to save, since they can be modified during callee
- `jr ra`: when some functions is called somewhere, in order to continue running programme, use jr ra to move to ra, and continue programme. 用于返回。 和 `jal`配合使用
- `jal`: jumo and link, 给 `ra`赋值， 这样函数就知道回到哪里了
- `j`: jal x0 offset 不记录返回值

### Immediates

- An I-type instruction can only have 12 bits of immediate
  - Bits 31:12 get the same value as Bit 11
- RISC-V immediates are "sign extended"
  - 首位为符号位



### Little Eddiem

- ![](https://snz04pap002files.storage.live.com/y4mT2S6RrFDKQJmTzbD0r-zFNd4ybshNaFhCiNnpCpgDarK_egSY5EirDubpuId2ljjU4s4rSfQ7thLe7LW5FI86KIudWsqymgp_6coO0D4jCGkYnc6cUPBF0DratTKLlZ8UJV2LWBZJgt_QXby0mOCcC65TDrXzgpe1kni14q0dYHvpVDyKzwcXTkF2nRSYnR9?width=2098&height=1184&cropmode=none)
  - 小端序： ![](https://upload.wikimedia.org/wikipedia/commons/thumb/e/ed/Little-Endian.svg/280px-Little-Endian.svg.png)
  - 自下而上的存储， 前面的在下面。 寄存器从下而上数。
  - 0x8f5 为负数(itype 指令只有12位， 因此大于 0x7ff 的都是负数)， 因此扩写成16位(因为寄存器为32位)的补码
    - 0xfffff8f5 (两者取反 加一 值相同)

### Types

![](https://snz04pap002files.storage.live.com/y4m6bArYGFBiAHqyJvsp3OGJ_HLK-fgf4EJj5kOFUSGTWKBTkw0aQ6l-rkjdCHTfljQ430peJcBgMHS6i9BcNiWFyAlPFstoe5zNJV2jzYKJyNZH_IeyhxxUiAxyLuTEMgGdDiMYCPJ_Y63azFHjOv9oH_jPIlN9tcu5_b4x1PpZuBBMyXiEqjjdnmft_RCcrMr?width=1466&height=408&cropmode=none)

- `I type `只能处理12 bit 立即数
- 

- LI = LUI + ADDI
  - ![](https://snz04pap002files.storage.live.com/y4muP-145WsnADoJQkCOu408FSE_5yYyr0iryC-6wvNTJ7G3eZ8CthGBhm6IVdGRCuXZZrcKP4mepPgDQnpFnpEzAO8TvrEq6I4toAEjuMv0-tcjy2X7wF_fNKCqq-7togjeBqhZaOn8w6RS8mI_KZqXwKue9dV95mv__AWzFy4vyAt2oSKcT6FttPN-o-gGmhW?width=1770&height=990&cropmode=none)
  - ![](https://snz04pap002files.storage.live.com/y4mUTd2NJ5QIKRlWd0NtMbOYNirRqFBlLUndMQPTIi9_fqaDFlbgM7WiIeSKITRzqE1ecF22d0wQFSDZxhijaq8nfzNr6MRWbQQ_EVMwXt81b1SpQ_glRN6pfgCAdMo0ZQ9nM9_J5urBw7-dHTI7v-3gGKoj591FHQT_EEVcUBZ8H65UMy9wlxqNs3Et6XY-_En?width=1770&height=1328&cropmode=none)
  - **DEADBEEF**: 如图所示，出现了问题。当且仅当32bit的后12bit为负数时，进行addi操作会扩展成补码。例如 `0xEFF`会扩展成`0xFFFFFEFF`。对于前20bit来说， 加上 `0xFFFFF000`无疑等于对第12位取反。
  - 解决方法是， 当后三位大于 `0x7FF`的时候，对第12位+1， 或者是对`x10`+1。 因为第12位是对于16进制的剩余类，在这个剩余类中，`0+F=F, F+1=0`, 取反加1为自身。

### REF

[Risc-V instructions Set](https://1drv.ms/b/s!Au3reWMu7K2ChOZfWdNGg9fNARrDAA?e=QDam4p)

