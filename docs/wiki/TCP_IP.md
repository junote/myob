- ARP（Address Resolve Protocol，地址解析协议）
- RARP（Reverse Address Resolve Protocol，逆地址解析协议）
- ICMP协议（Internet Control Message Protocol，因特网控制报文协议）
- TCP协议（Transmission Control Protocol，传输控制协议）
- UDP协议（User Datagram Protocol，用户数据报协议）
- SCTP协议（Stream Control Transmission Protocol，流控制传输协议）
- OSPF（Open Shortest Path First，开放最短路径优先）
- DNS（Domain Name Service，域名服务）
- MTU Max Transmit Unit 最大传输单元
##### 协议族
![[Pasted image 20240621203827.png]]

##### 封装
![[Pasted image 20240621204525.png]]

###### ethernet framer
![[Pasted image 20240621204729.png]]


##### arp
ARP协议能实现任意网络层地址到任意物理地址的转换
其工作原理是：主机向自己所在的网络广播一个ARP请求，该请求包含目标机器的网络地址。此网络上的其他机器都将收到这个请求，但只有被请求的目标机器会回应一个ARP应答，其中包含自己的物理地址。
![[Pasted image 20240621205040.png]]

- 硬件类型字段定义物理地址的类型，它的值为1表示MAC地址。
- 协议类型字段表示要映射的协议地址类型，它的值为0x800，表示IP地址。
- 硬件地址长度字段和协议地址长度字段，顾名思义，其单位是字节。对MAC地址来说，其长度为6；对IP（v4）地址来说，其长度为4。
- 操作字段指出4种操作类型：ARP请求（值为1）、ARP应答（值为2）、RARP请求（值为3）和RARP应答（值为4）。
- 最后4个字段指定通信双方的以太网地址和IP地址。
