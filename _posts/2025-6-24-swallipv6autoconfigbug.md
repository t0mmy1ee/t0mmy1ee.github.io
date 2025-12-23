---
title: ipv6协议一致性测试中autoconfig 2.2
description: 解决软件WAF在ipv6协议一致性测试autoconfig 2.2测试项中遇到的问题
author: wangxin
date: 2025-6-24 17:41:00 +0800
categories: [测试工具]
tags: [IPv6协议一致性, IxANVL]
#pin: true
#math: true
#mermaid: true
#image:
  #path: /assets/img/2025-12-21-15-24-36.png
---

软件waf安装在虚拟机中，与一致性测试虚拟机在同一物理主机中时，测试ipv6一致性autoconfig 2.2时遇到以下问题，可能是受到了网络中其他设备的干扰  
![](/assets/img/2025-12-23-09-19-31.png)  
需要改变组网方式，将waf虚拟机与一致性测试虚拟机连接到一个独立的虚拟网卡，不能桥接任何物理网卡，可能会有干扰  
![](/assets/img/2025-12-23-09-20-07.png)  
Waf虚拟机：  
![](/assets/img/2025-12-23-09-20-34.png)  
一致性测试虚拟机：  
![](/assets/img/2025-12-23-09-21-15.png)  
![](/assets/img/2025-12-23-09-21-28.png)