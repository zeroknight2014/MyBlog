---
title: Android下调用lua去读写文件的权限问题
date: 2016-05-26 12:52:27
keywords: Android lua SEAndroid 安全上下文
category: Lua
tags: [Android, lua, SEAndroid]
description: <!--more-->
---

**由于工作中需要在Android下调用lua去执行一些逻辑操作，并且在lua中会调用shell采用root权限去执行一些命令。例如：采用unzip命令从zip中去解压某个文件出来，并读取文件的内容。**

* 4.4以下，selinux默认不开启的情况下，采用root权限解压出来的文件，用io.read()是正常的；
* 4.4以上，selinux开启后，root权限解压到data分区app数据目录下的文件是没有对应的安全上下文，这个时候app调用lua的io.read()是无法打开文件的，必须在解压文件后给文件赋予对应的安全上下文。

**data分区下修改安全上下文：**
>android sdk < 23时： chcon u:object_r:app_data_file:s0 filepath
>android sdk >= 23时：chcon u:object_r:app_data_file:s0:c512,c768 filepath

要保证文件对应的路径中的所有目录都有对应的安全上下文，否则也会打开失败

**PS: 4.4以上系统，采用shell去操作一些文件的时候尤其需要注意类似的安全上下文问题**