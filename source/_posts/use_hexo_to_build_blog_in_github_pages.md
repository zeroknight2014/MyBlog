layout: 使用hexo搭建博客并部署到github
title: 使用hexo搭建博客并部署到github
date: 2016-08-03 22:03:02
tags: hexo github
description: <!--more-->
---


## 什么是hexo
> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。


## 如何安装hexo
1. 安装Node.js
2. 安装Git
3. `$  npm install -g hexo-cli`

## 初次搭建博客
1. 在合适的目录下创建博客
`$ hexo init <folder>`
`$ npm install`

## 安装自己喜欢的主题
推荐一个主题NexT，安装方法：
```shell
$ cd your-hexo-site 
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
```
启用主题： 修改根目录的_config.yml中 `theme: next`

## 更改配置
1. 博客的相关配置在根目录的_config.yml中
2. 主题的配置在themes/next/_config.yml中
3. 详细配置，[看这里](http://theme-next.iissnan.com/getting-started.html)

## 保存配置
只需要保存source目录，及根目录的_config.yml和主题目录下的_config.yml，
在更换电脑时只要覆盖对应的文件即可；
当然也可以直接将所有的文件一起打包保存。

### 一些坑
1. 如果出现hexo d时，出现`ERROR Deployer not found: git`错误，请安装`npm install hexo-deployer-git --save`
2. _config.yml的deploy配置：type必须是git， resposity必须是`https://开头`， type/repo/branch都必须是前面两个空格，冒号后一个空格
