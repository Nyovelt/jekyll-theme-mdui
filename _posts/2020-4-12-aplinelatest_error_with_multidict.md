---
layout: post
title: "一次关于alphine和pip的docker build失败解决"
date: 2020-4-12 12:00:59 +0800
category: Technology
tags: Linux Docker
img: https://7mqbqw.sn.files.1drv.com/y4msL9leOSEeOD5tisvwXa-7YDFh524YJd-Vzv4VJ2Hu6r75sUBTkEu30isecaUyBliVKSm8ngykmLcbHxphdEDh39oszMA5ZNY7BVx6-pqT0mKLCXqE-Zw8Vv22WC0GnASj_b5rF3-VuCia6vWC5vyw-acZ7reaxLhC2wv87Z6LjJPCwCYIiMSVp5PLBkSI06rz9w9rc8WVbSURh0mBkw0hA?width=650&height=450&cropmode=none
describe: Building wheel for multidict (PEP 517) -finished with status 'error' 
---


## 起因

时隔五个月之后，接手镜像站维护的我准备开始为学校的镜像站([外网访问](https://mirrors-wan.geekpie.club/)  [通用访问)](https://mirrors.geekpie.club/)增加新的镜像时遇到了问题。我们的镜像同步脚本是放在Docker里的，而做了改动之后过不了Docker Build这关。

## 问题一

### 错误

起初的 dockerfile :

```dockerfile
FROM alpine:latest

MAINTAINER zxt @ Geek Pie Association

RUN apk update && apk add --no-cache git python3 rsync zsh findutils wget util-linux grep && pip3 install apscheduler bandersnatch

RUN mkdir /Mirrors-AutoSync

COPY Mirrors-AutoSync.conf Mirrors-AutoSync.py bandersnatch.conf /Mirrors-AutoSync/

COPY script /Mirrors-AutoSync/script
COPY quick-fedora-mirror.conf /Mirrors-AutoSync/script/
RUN find /Mirrors-AutoSync/script/ -regex '[^\.]*[^/]' | xargs chmod +x
RUN find /Mirrors-AutoSync/script/ -regex '[^\.]*\.sh' | xargs chmod +x

CMD python3 /Mirrors-AutoSync/Mirrors-AutoSync.py
```

在build过程中遇到了:

```

Building wheels for collected packages: multidict

  Building wheel for multidict (PEP 517): started

  Building wheel for multidict (PEP 517): finished with status 'error'

[91m  ERROR: Command errored out with exit status 1:
   command: /usr/bin/python3.8 /usr/lib/python3.8/site-packages/pip/_vendor/pep517/_in_process.py build_wheel /tmp/tmpsqe3esd5
       cwd: /tmp/pip-install-bqnuhmm7/multidict
```

关键错误在于:

```
 gcc -Wno-unused-result -Wsign-compare -DNDEBUG -g -fwrapv -O3 -Wall -Os -fomit-frame-pointer -g -Os -fomit-frame-pointer -g -Os -fomit-frame-pointer -g -DTHREAD_STACK_SIZE=0x100000 -fPIC -I/usr/include/python3.8 -c multidict/_multidict.c -o build/temp.linux-x86_64-3.8/multidict/_multidict.o -O2 -std=c99 -Wall -Wsign-compare -Wconversion -fno-strict-aliasing -pedantic
  error: command 'gcc' failed with exit status 1
```

### 解决方案

报错是因为缺少 gcc ，所以在 dockerfile 中加入:

```dockerfile
RUN apk add gcc
```

变成

```dockerfile
RUN apk update && apk add --no-cache gcc git python3 rsync zsh findutils wget util-linux grep && pip3 install apscheduler bandersnatch
```

#### 相关issue

https://github.com/pydata/bottleneck/issues/281

https://github.com/custom-components/alexa_media_player/issues/526

https://stackoverflow.com/questions/59539137/how-can-i-fix-gcc-error-when-install-multidict-in-alpine-docker-container

## 问题二

### 错误

安装完 gcc 后更多的错误显现了出来:

```
  multidict/_multidict.c:1:10: fatal error: Python.h: No such file or directory
      1 | #include "Python.h"
        |          ^~~~~~~~~~
  compilation terminated.
  error: command 'gcc' failed with exit status 1
```



```
  /usr/include/python3.8/Python.h:11:10: fatal error: limits.h: No such file or directory
     11 | #include <limits.h>
        |          ^~~~~~~~~~
  compilation terminated.
  error: command 'gcc' failed with exit status 1
  ----------------------------------------
```

```
    In file included from src/lxml/etree.c:692:
    src/lxml/includes/etree_defs.h:14:10: fatal error: libxml/xmlversion.h: No such file or directory
       14 | #include "libxml/xmlversion.h"
          |          ^~~~~~~~~~~~~~~~~~~~~
    compilation terminated.
    Compile failed: command 'gcc' failed with exit status 1
    creating tmp
    cc -I/usr/include/libxml2 -c /tmp/xmlXPathInit73_7300k.c -o tmp/xmlXPathInit73_7300k.o
    /tmp/xmlXPathInit73_7300k.c:1:10: fatal error: libxml/xpath.h: No such file or directory
        1 | #include "libxml/xpath.h"
          |          ^~~~~~~~~~~~~~~~
    compilation terminated.
    *********************************************************************************
    Could not find function xmlCheckVersion in library libxml2. Is libxml2 installed?
    *********************************************************************************
    error: command 'gcc' failed with exit status 1
```

### 解决方案

问题原因是缺少相应的运行库（而且缺的很多）。经过搜索，在 dockerfile 中添加相应的运行库，同时升级 pip 版本。

最后的 dockerfile 变成:

```dockerfile
FROM alpine:latest

MAINTAINER zxt @ Geek Pie Association

RUN apk update \
        && apk add --no-cache gcc git python3-dev musl-dev linux-headers  libc-dev  rsync zsh \
        	findutils wget util-linux grep libxml2-dev libxslt-dev \
        &&  pip3 install --upgrade pip  \
        &&  pip3 install apscheduler bandersnatch

RUN mkdir /Mirrors-AutoSync

COPY Mirrors-AutoSync.conf Mirrors-AutoSync.py bandersnatch.conf /Mirrors-AutoSync/

COPY script /Mirrors-AutoSync/script
COPY quick-fedora-mirror.conf /Mirrors-AutoSync/script/
RUN find /Mirrors-AutoSync/script/ -regex '[^\.]*[^/]' | xargs chmod +x
RUN find /Mirrors-AutoSync/script/ -regex '[^\.]*\.sh' | xargs chmod +x

CMD python3 /Mirrors-AutoSync/Mirrors-AutoSync.py
```



## 总结

错误的产生原因大概要 alpine 背。

## 相关文件
[Mirrors-AutoSync](https://github.com/ShanghaitechGeekPie/Mirrors-AutoSync)