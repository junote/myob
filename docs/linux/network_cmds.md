### ip

sudo apt install iproute2

 ip -d link show


### ifconfig

```
ifconfig eth1 hw ether 00:01:9f:ef:66:77
```
### arp
arp-d 192.168.1.109#删除Kongming20对应的ARP缓存项
arp-s 192.168.1.109 08:00:27:53:10:67#添加Kongming20对应的ARP缓存项

### tcpdump

tcpdump是一款经典的网络抓包工具。

❑-n，使用IP地址表示主机，而不是主机名；使用数字表示端口号，而不是服务名称。

❑-i，指定要监听的网卡接口。“-i any”表示抓取所有网卡接口上的数据包。

❑-v，输出一个稍微详细的信息，例如，显示IP数据包中的TTL和TOS信息。

❑-t，不打印时间戳。

❑-e，显示以太网帧头部信息。

❑-c，仅抓取指定数量的数据包。

❑-x，以十六进制数显示数据包的内容，但不显示包中以太网帧的头部信息。

❑-X，与-x选项类似，不过还打印每个十六进制字节对应的ASCII字符。

❑-XX，与-X相同，不过还打印以太网帧的头部信息。

❑-s，设置抓包时的抓取长度

❑-S，以绝对值来显示TCP报文段的序号，而不是相对值。

❑-w，将tcpdump的输出以特殊的格式定向到某个文件。

❑-r，从文件读取数据包信息并显示之。

❑类型，解释其后面紧跟着的参数的含义。tcpdump支持的类型包括host、net、port和portrange。它们分别指定主机名（或IP地址），用CIDR方法表示的网络地址，端口号以及端口范围。
` $tcpdump net 1.2.3.0/24`

❑方向，src指定数据包的发送端，dst指定数据包的目的端。`$tcpdump dst port 13579`

❑协议，指定目标协议。`tcpdump icmp`


cpdump支持的逻辑操作符和编程语言中的逻辑操作符完全相同，包括and（或者＆＆）、or（或者||）、not（或者!）。
`tcpdump -i eth0 -ent '(dst 192.168.1.109 and src 192.168.1.108)or (dst 192.168.1.108 and src 192.168.1.109)'


`tcpdump-i eth0 -nt -s 500 port domain`
用“port domain”来过滤数据包，表示只抓取使用domain（域名）服务的数据包，即DNS查询和应答报文。

`tcpdump -ntv -i eth0 icmp`
只抓取ICMP报文

tcpdump还允许直接使用数据包中的部分协议字段的内容来过滤数据包。比如，仅抓取TCP同步报文段，可使用如下命令

```
tcpdump'tcp[13]＆2!=0'
tcpdump'tcp[tcpflags]＆tcp-syn!=0'


tcpdump -vv -i eth1 '( vlan and ( ether[14:2] & 0xfff == 1000 or ether[14:2] & 0xfff == 501 ) ) and ( ip host 10.1.1.98 or ip host 10.1.1.99 )'
```



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
nc（netcat）命令短小精干、功能强大，有着“瑞士军刀”的美誉。
主要被用来快速构建网络连接。我们可以让它以服务器方式运行，监听某个端口并接收客户连接，因此它可用来调试客户端程序。
使之以客户端方式运行，向服务器发起连接并收发数据，因此它可以用来调试服务器程序，此时它有点像telnet程序。

nc命令常用的选项包括：

❑-i，设置数据包传送的时间间隔。
❑-l，以服务器方式运行，监听指定的端口。nc命令默认以客户端方式运行。
❑-k，重复接受并处理某个端口上的所有连接，必须与-l选项一起使用。
❑-n，使用IP地址表示主机，而不是主机名；使用数字表示端口号，而不是服务名称。
❑-p，当nc命令以客户端方式运行时，强制其使用指定的端口号。3.4.2小节中我们就曾使用过该选项。
❑-s，设置本地主机发送出的数据包的IP地址。
❑-C，将CR和LF两个字符作为行结束符。
❑-U，使用UNIX本地域协议通信。
❑-u，使用UDP协议。nc命令默认使用的传输层协议是TCP协议。
❑-w，如果nc客户端在指定的时间内未检测到任何输入，则退出。
❑-X，当nc客户端和代理服务器通信时，该选项指定它们之间使用的通信协议。目前nc支持的代理协议包括“4”（SOCKS v.4），“5”（SOCKS v.5）和“connect”（HTTPS proxy）。nc默认使用的代理协议是SOCKS v.5。
❑-x，指定目标代理服务器的IP地址和端口号。比如，要从Kongming20连接到ernest-laptop上的squid代理服务器，并通过它来访问www.baidu.com的Web服务，可以使用如下命令：
```
$nc-x ernest-laptop:1080-X connect www.baidu.com 80
```
❑-z，扫描目标机器上的某个或某些服务是否开启（端口扫描）。比如，要扫描机器ernest-laptop上端口号在20～50之间的服务，可以使用如下命令：`$nc-z ernest-laptop 20-50`

### netstat
netstat是一个功能很强大的网络信息统计工具。它可以打印本地网卡接口上的全部连接、路由表信息、网卡接口信息等

❑-n，使用IP地址表示主机，而不是主机名；使用数字表示端口号，而不是服务名称。
❑-a，显示结果中也包含监听socket。
❑-t，仅显示TCP连接。
❑-r，显示路由信息。
❑-i，显示网卡接口的数据流量。
❑-c，每隔1 s输出一次。
❑-o，显示socket定时器（比如保活定时器）的信息。
❑-p，显示socket所属的进程的PID和名字。

netstat的每行输出都包含如下6个字段

❑Proto，协议名。
❑Recv-Q，socket内核接收缓冲区中尚未被应用程序读取的数据量。
❑Send-Q，未被对方确认的数据量。\
❑Local Address，本端的IP地址和端口号。
❑Foreign Address，对方的IP地址和端口号。
❑State，socket的状态。对于无状态协议，比如UDP协议，这一字段将显示为空。而对面向连接的协议而言，netstat支持的State包括ESTABLISHED、SYN_SENT、SYN_RCVD、FIN_WAIT1、FIN_WAIT2、TIME_WAIT、CLOSE、CLOSE_WAIT、LAST_ACK、LISTEN、CLOSING、UNKNOWN。它们的含义和图3-8中的同名状态一致 [1] 。


### iperf

iperf是一个测量网络状况的工具，-s选项表示将其作为服务器运行。

服务端：收包，使用 -s 参数指定， iperf3 -s
客户端：发包，使用 -c xx.xx.xx.xx 来指定要往哪个服务端发包， iperf3 -c 172.20.20.200
iperf3 还有更多的参数，其中有一些是客户端专用的，有一些是服务端专用的，也有一些是二者共用的。

具体可以前往这个地址，进行查阅：https://www.cnblogs.com/yingsong/p/5682080.html

常用的参数有

-u：发送 UDP 包，仅客户端可用，服务端默认 tcp udp 都可以接收
-b：指定发送速率（比如 100M），发送端不受限速影响，如果有限速，也只是接收端有影响
-p：后接服务端监听的端口
-i：设置带宽报告的时间间隔，单位为秒
-t：设置测试的时长，单位为秒
-w：设置tcp窗口大小，一般可以不用设置，默认即可
-B：绑定客户端的ip地址
-4：指定 ipv4
-n：指定传输的字节数
-f：格式化带宽数输出，后接单位，比如 K，M
--get-server-output：在客户端直接获取服务端输出的结果

iperf3 的输出结果可以分为两类

一类是，详细的带宽数据
一类是，最终的带宽数据
如果你像我一样加上 --get-server-output ，可以看到服务端输出的报告。

接下来看一下，输出的报告有哪些内容。

第一列 Interval：测试的时长
第二列 Transfer：在 Interval 时长里，传输的数据量
第三列 Bitrate：传输速率
第四列 Jitter：网络抖动，连续发送数据包时延差值的平均值，越小说明网络质量越好
第五列 Lost/Total Datagrams：丢失的数据包与发送的总数据包


### lsof

lsof（list open file）是一个列出当前系统打开的文件描述符的工具。
-i，显示socket文件描述符。
-u，显示指定用户启动的所有进程打开的所有文件描述符。
-c，显示指定的命令打开的所有文件描述符。`lsof-c websrv`
-p，显示指定进程打开的所有文件描述符。
-t，仅显示打开了目标文件描述符的进程的PID。


lsof命令的输出内容相当丰富，其中每行内容都包含如下字段：

❑COMMAND，执行程序所使用的终端命令（默认仅显示前9个字符）。
❑PID，文件描述符所属进程的PID。
❑USER，拥有该文件描述符的用户的用户名。
❑FD，文件描述符的描述。其中cwd表示进程的工作目录，rtd表示用户的根目录，txt表示进程运行的程序代码，mem表示直接映射到内存中的文件（本例中都是动态库）。有的FD是以“数字+访问权限”表示的，其中数字是文件描述符的具体数值，访问权限包括r（可读）、w（可写）和u（可读可写）。在本例中，0u、1u、2u分别表示标准输入、标准输出和标准错误输出；3u表示处于LISTEN状态的监听socket；4u表示epoll内核事件表对应的文件描述符。
❑TYPE，文件描述符的类型。其中DIR是目录，REG是普通文件，CHR是字符设备文件，IPv4是IPv4类型的socket文件描述符，0000是未知类型。更多文件描述符的类型请参考lsof命令的man手册，这里不再赘述。
❑DEVICE，文件所属设备。对于字符设备和块设备，其表示方法是“主设备号，次设备号”。由代码清单17-1可见，测试机器上的程序文件和动态库都存放在设备“8,3”中。其中，“8”表示这是一个SCSI硬盘；“3”表示这是该硬盘上的第3个分区，即sda3。websrv程序的标准输入、标准输出和标准错误输出对应的设备是“136,3”。
其中，“136”表示这是一个伪终端；“3”表示它是第3个伪终端，即/dev/pts/3。关于设备编号的更多细节，请参考文档http：//www.kernel.org/pub/linux/docs/lanana/device-list/devices-2.6.txt。对于FIFO类型的文件，比如管道和socket，该字段将显示一个内核引用目标文件的地址，或者是其i节点号。
❑SIZE/OFF，文件大小或者偏移值。如果该字段显示为“0t*”或者“0x*”，就表示这是一个偏移值，否则就表示这是一个文件大小。对字符设备或者FIFO类型的文件定义文件大小没有意义，所以该字段将显示一个偏移值。
❑NODE，文件的i节点号。对于socket，则显示为协议类型，比如“TCP”。
❑NAME，文件的名字。



### strace

strace是测试服务器性能的重要工具。它跟踪程序运行过程中执行的系统调用和接收到的信号，并将系统调用名、参数、返回值及信号名输出到标准输出或者指定的文件。

strace命令常用的选项包括：
❑-c，统计每个系统调用执行时间、执行次数和出错次数。
❑-f，跟踪由fork调用生成的子进程。
❑-t，在输出的每一行信息前加上时间信息。
❑-e，指定一个表达式，用来控制如何跟踪系统调用
	◆-e trace=set，只跟踪指定的系统调用。例如，-e trace=open，close，read，write表示只跟踪open、close、read和write这四种系统调用。
	◆-e trace=file，只跟踪与文件操作相关的系统调用。
	◆-e trace=process，只跟踪与进程控制相关的系统调用。
	◆-e trace=network，只跟踪与网络相关的系统调用。
	◆-e trace=signal，只跟踪与信号相关的系统调用。
	◆-e trace=ipc，只跟踪与进程间通信相关的系统调用。
	◆-e signal=set，只跟踪指定的信号。比如，-e signal=!SIGIO表示跟踪除SIGIO之外的所有信号。
	◆-e read=set，输出从指定文件中读入的数据。
❑-o，将strace的输出写入指定的文件。

strace命令对不同的参数类型将有不同的输出方式，

❑对于C风格的字符串，strace将输出字符串的内容。
❑对于结构体，strace将用“{}”输出该结构体的每个字段，并用“,”将每个字段隔开。
❑对于位集合参数（比如信号集类型sigset_t），strace将用“[]”输出该集合中所有被置1的位，并用空格将每一项隔开。

```
$./websrv 127.0.0.1 13579

$ps-ef|grep websrv

shuang 30526 29064 0 05:19 pts/7 00:00:00./websrv 127.0.0.1 13579

$sudo strace -p 30526

epoll_wait(4,

```

### vmstat

vmstat是virtual memory statistics的缩写，它能实时输出系统的各种资源的使用情况，比如进程信息、内存使用、CPU使用率以及I/O使用情况。

vmstat命令常用的选项和参数包括：

❑-f，显示系统自启动以来执行的fork次数。
❑-s，显示内存相关的统计信息以及多种系统活动的数量（比如CPU上下文切换次数）。
❑-d，显示磁盘相关的统计信息。
❑-p，显示指定磁盘分区的统计信息。
❑-S，使用指定的单位来显示。参数k、K、m、M分别代表1000、1024、1 000 000和1 048 576字节。
❑delay，采样间隔（单位是s），即每隔delay的时间输出一次统计信息。
❑count，采样次数，即共输出count次统计信息。

```
$vmstat 5 3#每隔5秒输出一次结果，共输出3次

procs-----------memory-------------swap-------io------system------cpu----
r b swpd free buff cache si so bi bo in cs us sy id wa
0 0 0 74864 48088 1486188 0 0 12 3 149 280 0 1 99 0
1 0 0 66548 48088 1494640 0 0 0 0 454 619 0 0 99 0
0 0 0 74608 48096 1486188 0 0 0 10 289 339 0 0 99 0
```

第1行输出是自系统启动以来的平均结果，而后面的输出则是采样间隔内的平均结果。vmstat的每条输出都包含6个字段

❑procs，进程信息。“r”表示等待运行的进程数目；“b”表示处于不可中断睡眠状态的进程数目。

❑memory，内存信息，各项的单位都是千字节（KB）。“swpd”表示虚拟内存的使用数量。“free”表示空闲内存的数量。“buff”表示作为“buffer cache”的内存数量。从磁盘读入的数据可能被保持在“buffer cache”中，以便下一次快速访问。“cache”表示作为“page cache”的内存数量。待写入磁盘的数据将首先被放到“page cache”中，然后由磁盘中断程序写入磁盘。

❑swap，交换分区（虚拟内存）的使用信息，各项的单位都是KB/s。“si”表示数据由磁盘交换至内存的速率；“so”表示数据由内存交换至磁盘的速率。如果这两个值经常发生变化，则说明内存不足。

❑io，块设备的使用信息，单位是block/s。“bi”表示从块设备读入块的速率；“bo”表示向块设备写入块的速率。

❑system，系统信息。“in”表示每秒发生的中断次数；“cs”表示每秒发生的上下文切换（进程切换）次数。

❑cpu，CPU使用信息。“us”表示系统所有进程运行在用户空间的时间占CPU总运行时间的比例；“sy”表示系统所有进程运行在内核空间的时间占CPU总运行时间的比例；“id”表示CPU处于空闲状态的时间占CPU总运行时间的比例；“wa”表示CPU等待I/O事件的时间占CPU总运行时间的比例。


### ifstat

fstat是interface statistics的缩写，它是一个简单的网络流量监测工具。其常用的选项和参数包括：

❑-a，监测系统上的所有网卡接口。

❑-i，指定要监测的网卡接口。

❑-t，在每行输出信息前加上时间戳。

❑-b，以Kbit/s为单位显示数据，而不是默认的KB/s。

❑delay，采样间隔（单位是s），即每隔delay的时间输出一次统计信息。

❑count，采样次数，即共输出count次统计信息。

```
$ifstat -a 2 5#每隔2秒输出一次结果，共输出5次

lo eth0
KB/s in KB/s out KB/s in KB/s out
8.62 8.62 124.71 515.74
7.46 7.46 125.50 510.30
1.79 1.79 126.87 497.57
8.10 8.10 127.82 526.13
9.53 9.53 130.10 516.78
```


### mpstat

mpstat是multi-processor statistics的缩写，它能实时监测多处理器系统上每个CPU的使用情况。mpstat命令和iostat命令通常都集成在包sysstat中，安装sysstat即可获得这两个命令

```
mpstat[-P{|ALL}][interval[count]]
```

- 选项P指定要监控的CPU号（0～CPU个数-1），其值“ALL”表示监听所有的CPU。
- interval参数是采样间隔（单位是s），即每隔interval的时间输出一次统计信息。
- count参数是采样次数，即共输出count次统计信息

❑CPU，指示该条信息是哪个CPU的数据。“0”表示是第1个CPU的数据，“1”表示是第2个CPU的数据，“all”则表示是这两个CPU数据的平均值。

❑%usr，除了nice值为负的进程，系统上其他进程运行在用户空间的时间占CPU总运行时间的比例。

❑%nice，nice值为负的进程运行在用户空间的时间占CPU总运行时间的比例。

❑%sys，系统上所有进程运行在内核空间的时间占CPU总运行时间的比例，但不包括硬件和软件中断消耗的CPU时间。

❑%iowait，CPU等待磁盘操作的时间占CPU总运行时间的比例。

❑%irq，CPU用于处理硬件中断的时间占CPU总运行时间的比例。

❑%soft，CPU用于处理软件中断的时间占CPU总运行时间的比例。

❑%steal，一个物理CPU可以包含一对虚拟CPU，这一对虚拟CPU由超级管理程序管理。当超级管理程序在处理某个虚拟CPU时，另外一个虚拟CPU则必须等待它处理完成才能运行。这部分等待时间就是所谓的steal时间。该字段表示steal时间占CPU总运行时间的比例。

❑%guest，运行虚拟CPU的时间占CPU总运行时间的比例。

❑%idle，系统空闲的时间占CPU总运行时间的比例。

### misc

#### check socket port
```

sudo apt install iproute2
sudo ss -tulpn
-t, --tcp: To see all TCP sockets
-u, --udp: To see all UDP sockets
-l, --listening: To see all listening sockets
-p, processes: To see which processes are using sockets
-n, --numeric: Use this option if you want to see a port number instead of service names


sudo apt-get install net-tools
sudo netstat –tulnp
-t, --tcp: To see all TCP sockets
-u, --udp: To see all UDP sockets
-l, --listening: To see all listening sockets
-p, processes: To see which processes are using sockets
-n, --numeric: Use this option if you want to see a port number instead of service names

apt-get install lsof
sudo lsof -nP -iTCP -sTCP:LISTEN

Check open ports using the Nmap utility
sudo apt install nmap
sudo nmap –sT –p-65535 ip-address
```


