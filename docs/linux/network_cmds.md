
### arp
arp-d 192.168.1.109#删除Kongming20对应的ARP缓存项
arp-s 192.168.1.109 08:00:27:53:10:67#添加Kongming20对应的ARP缓存项

### tcpdump

`tcpdump -i eth0 -ent '(dst 192.168.1.109 and src 192.168.1.108)or (dst 192.168.1.108 and src 192.168.1.109)'
用dst和src指定通信的目的端IP地址和源端IP地址
用-e选项开启以太网帧头部信息的显示

`tcpdump-i eth0 -nt -s 500 port domain`
用“port domain”来过滤数据包，表示只抓取使用domain（域名）服务的数据包，即DNS查询和应答报文。

`tcpdump -ntv -i eth0 icmp`
只抓取ICMP报文

``


### DNS
Linux使用/etc/resolv.conf文件来存放DNS服务器的IP地址。
```
nameserver 219.239.26.42
nameserver 124.207.160.106
```

host命令使用DNS协议和DNS服务器通信，其-t选项告诉DNS协议使用哪种查询类型。
```
$host-t A www.baidu.com
www.baidu.com is an alias for www.a.shifen.com.
www.a.shifen.com has address 119.75.217.56
www.a.shifen.com has address 119.75.218.77
```

### protocols
/etc/protocols文件定义了所有上层协议对应的protocol字段的数值。其中，ICMP是1，TCP是6，UDP是17。

### route
```
Kernel IP routing table
Destination Gateway Genmask Flags Metric Ref Use Iface
default 192.168.1.1 0.0.0.0 UG 0 0 0 eth0
192.168.1.0*255.255.255.0 U 1 0 0 eth0
```
![[Pasted image 20240622190145.png]]

```

$sudo route add-host 192.168.1.109 dev eth0

$sudo route del-net 192.168.1.0 netmask 255.255.255.0

$sudo route del default

$sudo route add default gw 192.168.1.109 dev eth0
```

### ip_forward

```
echo 1＞/proc/sys/net/ipv4/ip_forward
```
### ICMP
- /proc/sys/net/ipv4/conf/all/send_redirects内核参数指定是否允许发送ICMP重定向报文，
- /proc/sys/net/ipv4/conf/all/accept_redirects内核参数则指定是否允许接收ICMP重定向报文。

### tcp_port
/etc/services

### iptables
```
$sudo iptables -F
$sudo iptables -I INPUT -p tcp --syn -i eth0 -j DROP
```
丢弃所有接收到的连接请求


### sync_retries
/proc/sys/net/ipv4/tcp_syn_retries

### nc


### netstat


### iperf

iperf是一个测量网络状况的工具，-s选项表示将其作为服务器运行。
