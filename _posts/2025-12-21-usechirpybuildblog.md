---
title: 使用Chirpy搭建Blog
description: 这篇文章讲了如何配置 Chirpy 主题，适合新手入门
author: tianyuan
date: 2025-12-21 9:56:45 +0800
categories: [其他]
tags: [Chirpy, Blog]
#pin: true
#math: true
#mermaid: true
image:
  path: /assets/img/2025-12-21-09-58-43.png
---

# 1.部署Chirpy
Chirpy是Jekyll的一个模板，之前很早的时候使用过Jekyll，在github上搭建个人静态博客，感觉很好用（~~只不过后来太忙断更了~~）。但是美中不足的是没有搜索功能。后来看到了Chirpy，一个静态博客居然有搜索功能，真逆天！学了一下大概知道是预先建立索引的原理，利用Javascript匹配和展示搜索内容。<br>
扯远了，回到部署，最终要部署两套，一套是在github pages（github.io），另一套是在本地测试的环境（不然每次都推送到github有点太浪费时间了）。我按照Chirpy的[教程](https://chirpy.cotes.page/posts/getting-started/)尝试了一下，感觉人家教程里面推荐的方式已经是最合适小白的方式了。
## 部署GitHub仓库
![](/assets/img/2025-12-21-10-43-42.png)  
简单说就是人家把饭已经喂到嘴边了，按照步骤做，你自己的github.io的页面应该会很顺利就会显示，当然是一个只有框架的空壳。  
## 部署本地环境
![](/assets/img/2025-12-21-10-48-09.png)  
这里面主要要用到docker desktop、git、vscode及其插件。这个过程比想象中的要自动的多。  
1.用git把你的github仓库拉到本地目录  
2.打开docker desktop  
3.安装vscode，并装上dev container插件  
4.使用dev container插件的功能，用容器打开本地的项目目录，然后等待自动部署完成就行了  
![](/assets/img/2025-12-21-10-56-55.png)  
最后，在运行起来的容器里面，输入启动Jekyll的命令，就可以通过http://127.0.0.1:4000愉快的访问了。  
![](/assets/img/2025-12-21-14-45-59.png)

# 2.配置Chirpy
相比于第一步，第二步就更简单了，按照Chirpy的教程，对_config.yml等文件进行配置就行。值得一提的是如果要切换成中文，只要在_data/locales/路径下面放上对应的zh-CN.yml文件就行(这个可以在Chirpy的github库里面找到)。  
最后补充一点，github pages的build&deploy的校验比本地测试环境更严格。例如url里面有http的，也会造成build或url test不通过，导致无法正常发布。  

# 3.发布文章
这个就是写markdown文件就好了，现在VScode写markdown的插件蛮多，其中paste image强推，省事不少。

# 4.加入评论功能
Chirpy的示例里面有使用了giscus的评论功能，一查这东西是用了github的Disscussion功能，又是一个免费的好东西！那必须部署上。  
1.使用[giscus安装链接](https://github.com/apps/giscus/installations/new)在博客仓库上安装。  
2.然后使用[giscus配置页面](https://giscus.app/zh-CN)获取配置代码。
3.最后把生成的配置代码粘贴在Chirpy的_config.yml对应位置就行了。

**最后欢迎大家积极投稿~分享小工具、小技巧以及黑科技。**
