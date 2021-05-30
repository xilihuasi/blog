---
title: How to build Hexo
date: 2016-10-24 14:38:10
tags: [hexo]
categories: hexo
---

本教程默认你已经搭建好了 Node 和 Git 环境

## step 1

在控制台执行如下命令安装 hexo：

<!--more-->

npm install -g hexo

> _安装在当前用户目录下，是 hexo 的全局安装_

## step 2

执行初始化命令，初始化 hexo 到指定目录：
hexo init <folder>
或者 cd 到指定目录执行初始化命令：
hexo init

## step 3

cd 到你的初始化目录，执行如下命令生成静态页面至*hexo\public*目录：
hexo generate

> _命令完成后即可在*hexo\public*目录下看到当前日期默认生成的 html 文件，也可在*hexo\source\_\_posts*目录下看到 md 文件。不要急，下一步就可以看到 hexo 在浏览器中访问的效果啦。_

## step 4

执行如下命令启动本地服务：
hexo server
在浏览器中输入(http://localhost:4000) 就可以预览搭建成功的 hexo 啦！

## 发表第一篇文章

执行 new 命令新建文章，生成指定名称的文章至 hexo\source\ \_posts\postName.md：
hexo new [layout] "postName"
其中 layout 是可选参数，默认值为 post。在 scaffolds 目录下查看可选择的 layout，当然也可以新建或编辑。

![](http://ofjlqteaa.bkt.clouddn.com/Hexo-Step5.png)
