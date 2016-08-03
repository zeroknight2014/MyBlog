---
title: Android PIE验证导致可执行文件运行失败
date: 2016-05-26 14:21:39
category: Android
tags: [Android]
description: <!--more-->
---

### 可执行文件在Android中无法运行，错误提示如下:
>error: only position independent executables (PIE) are supported.


### 原因：
PIE这个安全机制从4.1引入，但是Android L之前的系统版本并不会去检验可执行文件是否基于PIE编译出的。因此不会报错。但是Android L已经开启验证，如果调用的可执行文件不是基于PIE方式编译的，则无法运行。


### 解决办法非常简单：
1.在Android.mk中加入如下flag就行。
```makefile
LOCAL_CFLAGS += -pie -fPIE
LOCAL_LDFLAGS += -pie -fPIE
```

2.PIE验证仅针对动态链接方式编译的可执行文件，如果编译时采用静态编译，则不会出现这种问题。