---
title: 鸿蒙Next安装Charles根证书
description: 为鸿蒙Next抓包做准备
author: tianyuan
date: 2025-4-10 11:20:00 +0800
categories: [检测工具, 移动应用]
tags: [安卓, Charles, HTTPS抓包, CA证书]
#pin: true
#math: true
#mermaid: true
#image:
#  path: /assets/img/2025-12-24-19-04-21.png
---
[【参考】Harmony Next charles 抓包指南_鸿蒙next微信抓包-CSDN博客](https://blog.csdn.net/jinmie0193/article/details/142212143)
## 1.下载charles根证书
![](/assets/img/2025-12-24-20-56-50.png)  
## 2.使用hdc连接手机导入证书  
手机下载文件夹路径：/storage/media/100/local/files/Docs/Download  
命令：hdc file send 【证书路径】 /storage/media/100/local/files/Docs/Download  
## 3.在手机上选取并安装证书
设置——隐私和安全——高级——证书与凭据——从存储设备安装——CA证书  
选取“我的手机”>Download  中对应的证书文件并安装。然后就能在用户CA证书里看到了。但由于不是系统CA证书，估计抓包效果还是有限，这个是后续的问题了。  
![](/assets/img/2025-12-24-20-58-17.png)