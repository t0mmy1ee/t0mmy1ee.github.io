---
title: IPv6协议一致性测试经验
description: 一些IPv6协议一致性测试过程中需要注意的要点
author: zhangqian
date: 2025-6-20 13:06:00 +0800
categories: [测试工具]
tags: [IPv6协议一致性, IxANVL]
#pin: true
#math: true
#mermaid: true
image:
  path: /assets/img/2025-12-23-09-32-30.png
---

> 首先，无论测试哪一项，除了测试仪要求配置的IPV6地址外，其他所有网卡、光卡、隔离卡上的IPV6地址都先清空。
{: .prompt-warning }  

1. 设置IP  
   ```text
   ip -6 addr add 3ffe:0:0:1::1/64 dev eth6  
   ip -6 addr add 4001::30/64 dev ens224  
   ip -6 addr add 4001::30/64 dev ens224  
   ip -6 addr add 3ffe::1:20c:29ff:fe80:aed1/64 dev eth1  
   ip -6 addr add 3ffe:4::1:20c:29ff:fe80:aed1/64 dev eth1
   ```
2. 删除IP  
   ```text  
   ip -6 addr del 3ffe:0:0:1::1/64 dev eth6  
   ```
3. 设置路由
   ```text  
   ip -6 route add 3ffe:0:0:2::/64 via 3ffe:0:0:1::2 dev eth6  
   ip -6 route add 3ffe:0:0:2::/64 via fe80::20c:29ff:fe80:aed2 dev eth1  
   ip -6 route add 3ffe:0:0:2::/64 via fe80::4f44:f939:5326:65c8 dev eth1  
   address 3ffe:0:0:2::/64 via gatewayfe80::4f44:f939:5326:65c8 on  
   ``` 
4. 删除路由
   ```text  
   ip -6 route del 3ffe:0:0:2::/64 via 3ffe:0:0:1::2 dev eth6  
   ```
5. 清理邻居缓存  
   ```text  
   ip -6 neigh flush dev eth6  
   ```  
6. 修改DupAddrDetectTransmits
   ```text  
   echo 1 >/proc/sys/net/ipv6/conf/ethx/dad_transmits  
   echo 2 >/proc/sys/net/ipv6/conf/ethx/dad_transmits  
   ```    
   **一般DUT系统默认是1** 
7. 设置MTU  
   ```text  
   ifconfig eth6 mtu 1500  
   ```  
8. 发ping请求包
   ```text  
   ping -s 1350 ff::05  
   ping -s 140 3ffe::2:2:ff:fe00:0  
   ```   
9. 初始化网卡
   ```text  
   echo 1 >/proc/sys/net/ipv6/conf/eth6/disable_ipv6  
   //echo 1 >/proc/sys/net/ipv6/conf/ens224/disable_ipv6  
   echo 0 >/proc/sys/net/ipv6/conf/eth6/disable_ipv6  
   // echo 0 >/proc/sys/net/ipv6/conf/ens224/disable_ipv6  
   echo 1 >/proc/sys/net/ipv6/conf/ens224/disable_ipv6 && echo 0 >/proc/sys/net/ipv6/conf/ens224/disable_ipv6  
   ```   
   **先1后0**  
   **过NDP的时候有时候初始化网卡是红色不通过的，可以换成down掉网卡，在up网卡nmcli con down ens224 && nmcli con up ens224;watch -n1 "ifconfig ens224"，一定要网卡起来后才点击OK，同时关注下仪表发出的IPv6地址，根据这个地址选择正确的IPv6地址 地址缺失时需使用nmcli connection modify ens224 ipv6.addr-gen-mode eui64快速链接**

10. 开启或关闭路由转发  
```text  
   echo 1 > /proc/sys/net/ipv6/conf/all/forwarding  
   ```  
   在icmp2.2用例中需要设置，同时提示添加任播地址时不需要做任何操作  
   ```text  
   echo 1 > /proc/sys/net/ipv6/conf/all/forwarding  
   ```     

# 注意
1. 检测仪要求DUT发起ping请求，DUT发起后，点击检测仪界面确定。检测仪页面输出框打印测试信息即可停止ping包。否则会影响其他检测项（如core第13.2项要求DUT发起ping请求。若13.2项通过后，ping请求仍在，接下来的14.1项会把收到的ping包当作本不该收到的ICMP回应。14.1检测内容为：测试仪发链路层不带IPV6标识的包给DUT,此时DUT应不做回应）
2. 除autoconfig中的检测项外，检测仪要求系统重启后，还需要重新配置下IPV6地址（检测开始时配置的地址），如PMTU中的每一个小项都要求重启DUT，若不是配置的永久IP，那么重启后还需要手动加IP。
3. autoconfig用一个DUT的网卡，其他四个检测任务用一个网卡，因为autoconfig检测过后，测试仪总会给曾经使用过的网卡分配当时检测用的IPV6地址。**（注意测试接口的地址获取是iface eth0 inet6 auto，千万别static）**
4. PMTY检测项中要求重启设备时，务必根据要求先点OK，再重启DUT。若顺序弄反，DUT网络状态很大几率显示unknown。此时检测仪发现无法与DUT通信，该项就会报failed标红，或者直接退出测试。补救方法：重启后ethtool ethx先确认网络状态是否正常，若不正常，立即重启，重启后网络状态即为正常。测试仪会等待5分钟给DUT重启，无论重启多少次，只要保证时在这5分钟内重启好就行。  

# 检测前配置
1. autoconfig
   ![](/assets/img/2025-12-23-13-02-36.png)  
   图中三个红框  
   第一个，只要是主机上存在的地址头就行（可以先在测试仪、DUT上按照这个格式配置一个IPV6地址，ping通就行），不要与图中下面检测仪、DUT中填写的一样。  
   第二个，DUT的mac地址一定要正确。  
   第三个，DUT的IPv6地址填写规则，如图中为例：  
   ![](/assets/img/2025-12-23-13-05-19.png)  
   请按照上面相同颜色的对应关系来填写DUT的IPV6地址。或者在启动测试后，测试仪也会根据上图填写的MAC把正确的IPV6地址分配给DUT，可以获取后填写再重新测试。
2. PMTU  
   ![](/assets/img/2025-12-23-13-06-07.png)  
   上图红框中：  
   第一个：同AUTOCONFIG。  
   第二个：填写配置的DUT的地址。  
   MAC地址按照实际修改，其他地方默认不变
3. PMTU2.2项ping命令需要加参数，如图：
   ![](/assets/img/2025-12-23-13-07-22.png)  
4. ICMP配置如下：
   ![](/assets/img/2025-12-23-13-08-00.png)  
   当测试到2.2，要求添加任播地址3ffe:0:0:1::时，不需要做任何操作，否则反而测试不同通过。同时开启路由转发功能echo 1 > /proc/sys/net/ipv6/conf/all/forwarding  
   Core 11.1不过，使用sysctl -w net.ipv6.auto_flowlabels=0进行修订  
   nmcli connection add type ethernet ifname ens224 con-name ens224（centos9，与用例无关）  
  NDP11.12不过，执行下列操作，同时注意下内核版本，尽量高一些  
  ![](/assets/img/2025-12-23-13-09-43.png)  
  此时初始化网卡使用下面的命令：（有时候autoconfig不过，也可以使用下面的命令，前提是停止NetworkManager服务）  
  ![](/assets/img/2025-12-23-13-10-26.png)
5. 以下命令可以提前配置好
   ```text  
   sysctl -w net.ipv6.conf.eth4.router_solicitations=3  
   sysctl -w net.ipv6.conf.bond0.router_solicitations=3  
   sysctl -w net.ipv6.conf.all.router_solicitations=3  
   sysctl -w net.ipv6.conf.eth4.accept_redirects=1  
   sysctl -w net.ipv6.conf.eth4.accept_ra=1  
   sysctl -w net.ipv6.conf.all.accept_redirects=1  
   sysctl -w net.ipv6.conf.all.accept_ra=1  
   sysctl -w net.ipv6.conf.all.accept_dad=1  
   ```