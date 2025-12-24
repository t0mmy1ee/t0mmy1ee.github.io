---
title: 使用iperf3测试网络性能
description: 没有ixia的时候，可以用iperf3来测试UDP吞吐量等性能
author: wangxin
date: 2025-7-11 17:02:00 +0800
categories: [检测工具, 性能测试]
tags: [iperf3, 吞吐量, UDP]
#pin: true
#math: true
#mermaid: true
image:
  path: /assets/img/2025-12-24-20-08-32.png
---
Windows系统下载iperf3安装包后无需安装直接解压即可，通过CMD进入安装目录使用  
下载链接：https://iperf.fr/iperf-download.php  
![](/assets/img2025-12-24-20-09-29.png)  
准备两台可以ping通的PC，分别作为服务端和客户端  
设置服务端：iperf3 -s（默认端口为5201，可通过 -p 指定端口）  
![](/assets/img2025-12-24-20-09-50.png)  
设置客户端:iperf3 -c 服务端IP（默认为tcp协议，可通过 -u 设置为udp协议）  
![](/assets/img2025-12-24-20-10-08.png)  
举例：udp测试-吞吐  
服务端：通过-p设置端口为5000  
![](/assets/img2025-12-24-20-10-28.png)  
客户端：通过-c 设置为udp协议，-p 连接服务端端口5000，-P 设置线程数，-l 设置只接受 1 次来自 Client 端的测试然后退出，-b 设置测试带宽，-t设置测试时间  
![](/assets/img2025-12-24-20-12-34.png)  
从服务端看吞吐量（Bitrate）、延迟（Jitter）和丢包情况（Lost/Total Datagrams）  
![](/assets/img2025-12-24-20-13-00.png)  
——其他常用参数  
```text  
-p, --port #，Server 端监听、Client 端连接的端口号； 
-f, --format [kmgKMG]，报告中所用的数据单位，Kbits, Mbits, KBytes, Mbytes； 
-i, --interval #，每次报告的间隔，单位为秒； 
-F, --file name，测试所用文件的文件名。如果使用在 Client 端，发送该文件用作测试；如果使用在 Server 端，则是将数据写入该文件，而不是丢弃； 
-A, --affinity n/n,m，设置 CPU 亲和力； 
-B, --bind ，绑定指定的网卡接口； 
-V, --verbose，运行时输出更多细节； 
-J, --json，运行时以 JSON 格式输出结果； 
--logfile f，输出到文件； 
-d, --debug，以 debug 模式输出结果； 
-v, --version，显示版本信息并退出； 
-h, --help，显示帮助信息并退出。 
Server 端参数： 
-s, --server，以 Server 模式运行； 
-D, --daemon，在后台以守护进程运行； 
-I, --pidfile file，指定 pid 文件； 
-1, --one-off，只接受 1 次来自 Client 端的测试，然后退出。 
Client 端参数 
-c, --client ，以 Client 模式运行，并指定 Server 端的地址； 
-u, --udp，以 UDP 协议进行测试； 
-b, --bandwidth #[KMG][/#]，限制测试带宽。UDP 默认为 1Mbit/秒，TCP 默认无限制； 
-t, --time #，以时间为测试结束条件进行测试，默认为 10 秒； 
-n, --bytes #[KMG]，以数据传输大小为测试结束条件进行测试； 
-k, --blockcount #[KMG]，以传输数据包数量为测试结束条件进行测试； 
-l, --len #[KMG]，读写缓冲区的长度，TCP 默认为 128K，UDP 默认为 8K； 
--cport ，指定 Client 端运行所使用的 TCP 或 UDP 端口，默认为临时端口； 
-P, --parallel #，测试数据流并发数量； 
-R, --reverse，反向模式运行（Server 端发送，Client 端接收）； 
-w, --window #[KMG]，设置套接字缓冲区大小，TCP 模式下为窗口大小； 
-C, --congestion ，设置 TCP 拥塞控制算法（仅支持 Linux 和 FreeBSD ）； 
-M, --set-mss #，设置 TCP/SCTP 最大分段长度（MSS，MTU 减 40 字节）； 
-N, --no-delay，设置 TCP/SCTP no delay，屏蔽 Nagle 算法； 
-4, --version4，仅使用 IPv4； 
-6, --version6，仅使用 IPv6； 
-S, --tos N，设置 IP 服务类型（TOS，Type Of Service）； 
-L, --flowlabel N，设置 IPv6 流标签（仅支持 Linux）； 
-Z, --zerocopy，使用 “zero copy”（零拷贝）方法发送数据； 
-O, --omit N，忽略前 n 秒的测试； 
-T, --title str，设置每行测试结果的前缀； 
--get-server-output，从 Server 端获取测试结果； 
--udp-counters-64bit，在 UDP 测试包中使用 64 位计数器（防止计数器溢出）。   
```   
