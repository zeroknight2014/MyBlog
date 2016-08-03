---
title: git多账户多站点使用
date: 2016-05-26 16:21:45
category: git
tags: git
description: <!--more-->
---


## 目标：实现git多账户多网站免账号密码登陆, 不弹密码输入框


## 问题：
*在公司的电脑上，公司项目的代码需要提交到内网部署的gitlab上，自己的项目需要提交到github上，而github使用的是自己的邮箱，gitlab使用的是公司邮箱，由于ssh免账号登陆需要在github和gitlab分别添加公钥，这个公钥由于邮箱不同所以必须各自准备一份，相应的项目提交时需要采用对应的用户名和邮箱及私钥，提交代码时需要反复进行输入账号密码*


## 解决方法：
### 1.取消git用户名和邮箱的全局设置
```git
git config --global --unset user.name
git config --global --unset user.email
```

### 2.通过邮箱生成对应网站需要的密钥
*github*:
```
ssh-keygen -t rsa -f id_rsa.github -C "yourname1@xxx.com"
```
*gitlab*:
```
ssh-keygen -t rsa -f id_rsa.gitlab -C "yourname2@xxx.com"
```
生成的文件中id_rsa.github.pub及id_rsa.gitlab.pub分别添加到各个网站中

### 3.在~目录下的.ssh文件夹中创建config文件，用来配置不同的host采用不同的username、email、id_rsa，配置如下:
```
Host gitlab.company.com
     Hostname gitlab.company.com  
     User gitlab_username
     IdentityFile ~/.ssh/id_rsa.gitlab
Host github.com
     Hostname github.com
     User github_username
     IdentityFile ~/.ssh/id_rsa.github
```
如果是linux，可能这么配置就可以了；
但是在windows上，我采用了TortoiseGit，然后按上述方法配置后，还是弹窗提示输入用户名和密码，第4步就可以解决该问题了

### 4.在windows环境变量中添加变量HOME，值为%USERPROFILE%，然后在%USERPROFILE%目录下添加_netrc文件（linux下为.netrc），配置如下:
```
machine github.com
login github_username
password  xxxxxxx

machine gitlab.company.com
login gitlab_username
password xxxxxxx
```