---
title: 三星机型cache和system打包img方法
date: 2016-05-27 21:24:48
keywords: Android、cache system
tags: [Android,刷机]
category: Android底层
description: <!--more-->
---


## 三星机型cache\system打包img方法


### system.img打包：


**4.4以下打包system.img**
>/data/local/tmp/make_ext4fs -s -l 2375680K -a system /sdcard/system.img.ext4 /system

**4.4及4.4以上打包system.img**
>/data/local/tmp/make_ext4fs -l 2375680K -s -S file_contexts -a /system /sdcard/system.img.ext4 /system



### cache.img打包:

>/system/bin/make_ext4fs -s -l 32768K -a cache /sdcard/cache.img /cache


### **(经过sgs4ext4fs 特殊处理)cache.img:**

>./sgs4ext4fs –bloat cache.img new_cache.img

1.如果是高通CPU，且机型是 A系列，比较新机型，如A5000，A3000，A7000，S5系列等则使用： 
高通CPU（新机型）cache.img.ext4 
2.如果是高通CPU和三星CPU，且百度看机型是比较老的，则使用，如三星i9300,i9308，n7100等： 
高通CPU和三星CPU（老机型如9300）cache.img 
3.如果是三星CPU，且比较新机型，如I9500，则使用： 
新三星cpu(如i9500)(经过sgs4ext4fs 特殊处理)cache.img 
4.如果是三星CPU，且是S6系列，则使用S6系列(经过sgs4ext4fs 特殊处理)cache.img；