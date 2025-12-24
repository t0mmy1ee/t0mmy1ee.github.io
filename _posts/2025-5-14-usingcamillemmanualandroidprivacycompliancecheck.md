---
title: 使用Camille手搓检测安卓APP个人信息安全合规
description: 学习安卓APP隐私合规检测工具的基本原理
author: tianyuan
date: 2025-5-14 20:07:00 +0800
categories: [检测工具, 移动应用]
tags: [安卓, 隐私合规, Camille, Frida, Hook, 堆栈]
#pin: true
#math: true
#mermaid: true
image:
  path: /assets/img/2025-12-24-19-04-21.png
---
[【参考1】Android：【1】一文教你使用Camille+夜神模拟器实现安卓应用隐私合规辅助检测_夜神模拟器过检测-CSDN博客](https://blog.csdn.net/Alex497259/article/details/127319790)  
[【参考2】Android APP隐私合规检测辅助工具Camille环境配置指引 - 知乎](https://zhuanlan.zhihu.com/p/500189682)  
[【参考3】GitHub - zhengjim_camille_ 基于Frida的Android App隐私合规检测辅助工具](https://github.com/zhengjim/camille)  
[【参考4】Android _ Frida • A world-class dynamic instrumentation toolkit](https://frida.re/docs/examples/android/)  

按照参考中的方式搭建起环境（1、2、3步）  

1. 手机端放frida-server（不多说了）
2. PC端安装frida-tools（参考中的好像已经过时了，只用下面一句）
   ![](/assets/img/2025-12-24-19-10-28.png)  
3. 按照【参考3】放camille并安装所需环境
   ```text  
   pip install -r requirements.txt  
   ```  
4. 按照【参考4】方式在手机端启动frida  
   ![](/assets/img/2025-12-24-19-16-06.png)  
   启动后能在进程里面看到已经启动了。  
   ![](/assets/img/2025-12-24-19-16-24.png)  
   然后在PC端测试，有如下效果说明frida运行正常  
   ![](/assets/img/2025-12-24-19-16-44.png)  
5. 然后使用camille测试，能够正常hook输出堆栈，但是有一点，就是点击功能好像没成功，不知道是不是包的依赖有问题……  
   ```text  
   python camille.py 【包名】  
   ```  
   ![](/assets/img/2025-12-24-19-18-33.png)  
   可以输出到xls  
   ```text  
   python camille.py com.snda.wifilocating -ns -f demo01.xls  
   ```  
6. 总之以上能够手搓检测，不用依赖捷兴工具也能测一些了……下一步可以研究下frida，自己写点hook脚本试试看，感觉有点搞明白APP检测工具的大致原理了。