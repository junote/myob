- ARP（Address Resolve Protocol，地址解析协议）
- RARP（Reverse Address Resolve Protocol，逆地址解析协议）
- ICMP协议（Internet Control Message Protocol，因特网控制报文协议）
- TCP协议（Transmission Control Protocol，传输控制协议）
- UDP协议（User Datagram Protocol，用户数据报协议）
- SCTP协议（Stream Control Transmission Protocol，流控制传输协议）
- OSPF（Open Shortest Path First，开放最短路径优先）
- DNS（Domain Name Service，域名服务）
- MTU Max Transmit Unit 最大传输单元
- BGP（Border Gateway Protocol，边际网关协议）
- RIP（Routing Information Protocol，路由信息协议）
### 协议族
![[Pasted image 20240621203827.png]]

### 封装
![[Pasted image 20240621204525.png]]

#### ethernet framer
![[Pasted image 20240621204729.png]]


### ARP
ARP协议能实现任意网络层地址到任意物理地址的转换
其工作原理是：主机向自己所在的网络广播一个ARP请求，该请求包含目标机器的网络地址。此网络上的其他机器都将收到这个请求，但只有被请求的目标机器会回应一个ARP应答，其中包含自己的物理地址。
![[Pasted image 20240621205040.png]]

- 硬件类型字段定义物理地址的类型，它的值为1表示MAC地址。
- 协议类型字段表示要映射的协议地址类型，它的值为0x800，表示IP地址。
- 硬件地址长度字段和协议地址长度字段，顾名思义，其单位是字节。对MAC地址来说，其长度为6；对IP（v4）地址来说，其长度为4。
- 操作字段指出4种操作类型：ARP请求（值为1）、ARP应答（值为2）、RARP请求（值为3）和RARP应答（值为4）。
- 最后4个字段指定通信双方的以太网地址和IP地址。

### DNS
![[Pasted image 20240622164242.png]]
- 16位标识字段用于标记一对DNS查询和应答，以此区分一个DNS应答是哪个DNS查询的回
- 16位标志字段用于协商具体的通信方式和反馈通信状态。
	- ![[Pasted image 20240622164422.png]]
	- QR，查询/应答标志。0表示这是一个查询报文，1表示这是一个应答报文
	- opcode，定义查询和应答的类型。0表示标准查询，1表示反向查询（由IP地址获得主机域名），2表示请求服务器状态。
	- AA，授权应答标志，仅由应答报文使用。1表示域名服务器是授权服务器。
	- TC，截断标志，仅当DNS报文使用UDP服务时使用。因为UDP数据报有长度限制，所以过长的DNS报文将被截断。1表示DNS报文超过512字节，并被截断。
	- RD，递归查询标志。1表示执行递归查询，即如果目标DNS服务器无法解析某个主机名，则它将向其他DNS服务器继续查询，如此递归，直到获得结果并把该结果返回给客户端。0表示执行迭代查询，即如果目标DNS服务器无法解析某个主机名，则它将自己知道的其他DNS服务器的IP地址返回给客户端，以供客户端参考。
	- RA，允许递归标志。仅由应答报文使用，1表示DNS服务器支持递归查询。
	- zero，这3位未用，必须都设置为0。
	- rcode，4位返回码，表示应答的状态。常用值有0（无错误）和3域名不存在
	
接下来的4个字段则分别指出DNS报文的最后4个字段的资源记录数目。对查询报文而言，它一般包含1个查询问题，而应答资源记录数、授权资源记录数和额外资源记录数则为0。应答报文的应答资源记录数则至少为1，而授权资源记录数和额外资源记录数可为0或非0。
- 查询名以一定的格式封装了要查询的主机域名。

### IP
IP协议是TCP/IP协议族的动力，它为上层协议提供**无状态**、**无连接**、**不可靠**的服务。
- 无状态（stateless）是指IP通信双方不同步传输数据的状态信息
- 无连接（connectionless）是指IP通信双方都不长久地维持对方的任何信息。
- 不可靠是指IP协议不能保证IP数据报准确地到达接收端，它只是承诺尽最大努力（best effort）

![[Pasted image 20240622170629.png]]

- 4位版本号（version）指定IP协议的版本。对IPv4来说，其值是4。
- 4位头部长度（header length）标识该IP头部有多少个32 bit字
- 8位服务类型（Type Of Service，TOS）
	- 一个3位的优先权字段（现在已经被忽略）
	- 4位的TOS字段
		- 最小延时
		- 最大吞吐量
		- 最高可靠性
		- 最小费用
	- 1位保留字段（必须置0）
- 16位总长度（total length）是指整个IP数据报的长度，以字节为单位，因此IP数据报的最大长度为65 535（216 -1）字节。但**由于MTU的限制，长度超过MTU的数据报都将被分片传输**，所以实际传输的IP数据报（或分片）的长度都远远没有达到最大值。
- 16位标识（identification）唯一地标识主机发送的每一个数据报。其初始值由系统随机生成；每发送一个数据报，其值就加1。
- 3位标志字段的
	- 第一位保留。
	- 第二位（Don’t Fragment，DF）表示“禁止分片”。如果设置了这个位，IP模块将不对数据报进行分片。在这种情况下，如果IP数据报长度超过MTU的话，IP模块将丢弃该数据报并返回一个ICMP差错报文。
	- 第三位（More Fragment，MF）表示“更多分片”。除了数据报的最后一个分片外，其他分片都要把它置1。
- 13位分片偏移（fragmentation offset）是分片相对原始IP数据报开始处（仅指数据部分）的偏移。实际的偏移值是该值左移3位（乘8）后得到的。由于这个原因，除了最后一个IP分片外，每个IP分片的数据部分的长度必须是8的整数倍
- 8位生存时间（Time To Live，TTL）是数据报到达目的地之前允许经过的路由器跳数。TTL值被发送端设置（常见的值是64）。数据报在转发过程中每经过一个路由，该值就被路由器减1。当TTL值减为0时，路由器将丢弃数据报，并向源端发送一个ICMP差错报文。
- 8位协议（protocol）用来区分上层协议
- 16位头部校验和（header checksum）由发送端填充，接收端对其使用CRC算法以检验IP数据报头部（注意，仅检验头部）在传输过程中是否损坏。
- 32位的源端IP地址和目的端IP地址用来标识数据报的发送端和接收端。
- IPv4最后一个选项字段（option）是可变长的可选信息。这部分最多包含40字节，因为IP头部最长是60字节（其中还包含前面讨论的20字节的固定部分）。可用的IP选项包括：
	- 记录路由（record route），告诉数据报途经的所有路由器都将自己的IP地址填入IP头部的选项部分，这样我们就可以跟踪数据报的传递路径。
	- 时间戳（timestamp），告诉每个路由器都将数据报被转发的时间（或时间与IP地址对）填入IP头部的选项部分，这样就可以测量途经路由之间数据报传输的时间。
	- 松散源路由选择（loose source routing），指定一个路由器IP地址列表，数据报发送过程中必须经过其中所有的路由器。
	- 严格源路由选择（strict source routing），和松散源路由选择类似，不过数据报只能经过被指定的路由器。
#### IP路由
![[Pasted image 20240622185625.png]]
- 当IP模块接收到来自数据链路层的IP数据报时，它首先对该数据报的头部做CRC校验，确认无误之后就分析其头部的具体信息。
- 如果该IP数据报的头部设置了源站选路选项（松散源路由选择或严格源路由选择），则IP模块调用数据报转发子模块来处理该数据报。
- 如果该IP数据报的头部中目标IP地址是本机的某个IP地址，或者是广播地址，即该数据报是发送给本机的，则IP模块就根据数据报头部中的协议字段来决定将它派发给哪个上层应用（分用）。
- 如果IP模块发现这个数据报不是发送给本机的，则也调用数据报转发子模块来处理该数据报。
- 数据报转发子模块将首先检测系统是否允许转发，如果不允许，IP模块就将数据报丢弃。如果允许，数据报转发子模块将对该数据报执行一些操作，然后将它交给IP数据报输出子模块。
- IP数据报应该发送至哪个下一跳路由（或者目标机器），以及经过哪个网卡来发送，就是IP路由过程
- IP模块实现数据报路由的核心数据结构是路由表。这个表按照数据报的目标IP地址分类，同一类型的IP数据报将被发往相同的下一跳路由器（或者目标机器）。

### ICMP
![[Pasted image 20240622190720.png]]

- 8位类型字段用于区分报文类型。它将ICMP报文分为两大类：一类是差错报文，这类报文主要用来回应网络错误，比如目标不可到达（类型值为3）和重定向（类型值为5）；另一类是查询报文，这类报文用来查询网络信息，比如ping程序就是使用ICMP报文查看目标是否可到达（类型值为8）的。
- 代码字段有4个可选值，用来区分不同的重定向类型。主机重定向代码值为1。
- ICMP协议的标准文档RFC 792。

### IPV6
![[Pasted image 20240622191311.png]]

- 4位版本号（version）指定IP协议的版本。对IPv6来说，其值是6。
- 8位通信类型（traffic class）指示数据流通信类型或优先级，和IPv4中的TOS类似。
- 20位流标签（flow label）是IPv6新增加的字段，用于某些对连接的服务质量有特殊要求的通信，比如音频或视频等实时数据传输。
- 16位净荷长度（payload length）指的是IPv6扩展头部和应用程序数据长度之和，不包括固定头部长度。
- 8位下一个包头（next header）指出紧跟IPv6固定头部后的包头类型,如扩展头（如果有的话）或某个上层协议头（比如TCP，UDP或ICMP）。
- 8位跳数限制（hop limit）和IPv4中的TTL含义相同。
- IPv6用128位（16字节）来表示IP地址

### TCP
**面向连接、字节流和可靠传输**
- TCP协议的这种连接是一对一的，所以基于广播和多播（目标是多个主机地址）的应用程序不能使用TCP服务。


- TCP模块发送出的TCP报文段的个数和应用程序执行的写操作次数之间没有固定的数量关系。
- 当接收端收到一个或多个TCP报文段后，TCP模块将它们携带的应用程序数据按照TCP报文段的序号（见后文）依次放入TCP接收缓冲区中，并通知应用程序读取数据。
- 字节流的概念：应用程序对数据的发送和接收是没有边界限制的。
- UDP则不然。发送端应用程序每执行一次写操作，UDP模块就将其封装成一个UDP数据报并发送之。接收端必须及时针对每一个UDP数据报执行读操作（通过recvfrom系统调用），否则就会丢包（这经常发生在较慢的服务器上）。

- TCP协议采用发送应答机制，即发送端发送的每个TCP报文段都必须得到接收方的应答
- TCP协议采用超时重传机制，发送端在发送出一个TCP报文段之后启动定时器，如果在定时时间内未收到应答，它将重发该报文段。
- TCP报文段最终是以IP数据报发送的，而IP数据报到达接收端可能乱序、重复，所以TCP协议还会对接收到的TCP报文段重排、整理，再交付给应用层。

![[Pasted image 20240622192914.png]]

- 16位端口号（port number）：告知主机该报文段是来自哪里（源端口）以及传给哪个上层协议或应用程序（目的端口）的。
- 32位序号（sequence number）：一次TCP通信（从TCP连接建立到断开）过程中某一个传输方向上的字节流的每个字节的编号。A发送给B的第一个TCP报文段中，序号值被系统初始化为某个随机值ISN（Initial Sequence Number，初始序号值）。那么在该传输方向上（从A到B），后续的TCP报文段中序号值将被系统设置成ISN加上该报文段所携带数据的第一个字节在整个字节流中的偏移。
- 32位确认号（acknowledgement number）：用作对另一方发送来的TCP报文段的响应。其值是收到的TCP报文段的序号值加1。
- 4位头部长度（header length）：标识该TCP头部有多少个32bit字
- 6位标志位
	- URG标志，表示紧急指针（urgent pointer）是否有效。
	- ACK标志，表示确认号是否有效。我们称携带ACK标志的TCP报文段为确认报文段。
	- PSH标志，提示接收端应用程序应该立即从TCP接收缓冲区中读走数据，为接收后续数据腾出空间（如果应用程序不将接收到的数据读走，它们就会一直停留在TCP接收缓冲区中）。
	- RST标志，表示要求对方重新建立连接。我们称携带RST标志的TCP报文段为复位报文段。
	- SYN标志，表示请求建立一个连接。我们称携带SYN标志的TCP报文段为同步报文段。
	- FIN标志，表示通知对方本端要关闭连接了。我们称携带FIN标志的TCP报文段为结束报文段。
- 16位窗口大小（window size）：是TCP流量控制的一个手段。这里说的窗口，指的是接收通告窗口（Receiver Window，RWND）。它告诉对方本端的TCP接收缓冲区还能容纳多少字节的数据，这样对方就可以控制发送数据的速度。
- 16位校验和（TCP checksum）：由发送端填充，接收端对TCP报文段执行CRC算法以检验TCP报文段在传输过程中是否损坏。注意，这个校验不仅包括TCP头部，也包括数据部分。
- 16位紧急指针（urgent pointer）：是一个正的偏移量。它和序号字段的值相加表示最后一个紧急数据的下一字节的序号。
- option
	- 选项的第一个字段kind说明选项的类型。有的TCP选项没有后面两个字段，仅包含1字节的kind字段。
	- 第二个字段length（如果有的话）指定该选项的总长度，该长度包括kind字段和length字段占据的2字节。
	- 第三个字段info（如果有的话）是选项的具体信息。
	![[Pasted image 20240622195836.png]]
		- kind=0是选项表结束选项。
		- kind=1是空操作（nop）选项，没有特殊含义，一般用于将TCP选项的总长度填充为4字节的整数倍。
		- kind=2是最大报文段长度选项。TCP连接初始化时，通信双方使用该选项来协商最大报文段长度（Max Segment Size，MSS）。TCP模块通常将MSS设置为（MTU-40）字节
		- kind=3是窗口扩大因子选项。TCP连接初始化时，通信双方使用该选项来协商接收通告窗口的扩大因子。假设TCP头部中的接收通告窗口大小是N，窗口扩大因子（移位数）是M，那么TCP报文段的实际接收通告窗口大小是N乘2M ，或者说N左移M位。注意，M的取值范围是0～14。我们可以通过修改/proc/sys/net/ipv4/tcp_window_scaling内核变量来启用或关闭窗口扩大因子选项。
		- kind=4是选择性确认（Selective Acknowledgment，SACK）选项。TCP通信时，如果某个TCP报文段丢失，则TCP模块会重传最后被确认的TCP报文段后续的所有报文段，这样原先已经正确传输的TCP报文段也可能重复发送，从而降低了TCP性能。
		- kind=5是SACK实际工作的选项。该选项的参数告诉发送方本端已经收到并缓存的不连续的数据块，从而让发送端可以据此检查并重发丢失的数据块。
		- kind=8是时间戳选项。该选项提供了较为准确的计算通信双方之间的回路时间（Round Trip Time，RTT）的方法，从而为TCP流量控制提供重要信息。我们可以通过修改/proc/sys/net/ipv4/tcp_timestamps内核变量来启用或关闭时间戳选项。
		- 
### TCP连接的建立和关闭
![[Pasted image 20240622194249.png]]

- 第1个TCP报文段包含SYN标志，因此它是一个同步报文段
- 第2个TCP报文段也是同步报文段
- 第3个TCP报文段是ernest-laptop对第2个同步报文段的确认

 - 从第3个TCP报文段开始，tcpdump输出的序号值和确认值都是相对初始ISN值的偏移。当然，我们可以开启tcpdump的-S选项来选择打印序号的绝对值。

- 第4个TCP报文段包含FIN标志，因此它是一个结束报文段
- 用TCP报文段5来确认该结束报文段。
- 发送自己的结束报文段6
- 用TCP报文段7给予确认

#### 半关闭状态
TCP连接是全双工的，所以它允许两个方向的数据传输被独立关闭。
通信的一端可以发送结束报文段给对方，告诉它本端已经完成了数据的发送，但允许继续接收来自对方的数据，直到对方也发送结束报文段以关闭连接。TCP连接的这种状态称为半关闭（half close）状态，
![[Pasted image 20240622194756.png]]
#### 连接超时
- TCP报文段的超时时间是32s（63s-16s-8s-4s-2s-1s）
- TCP模块一共执行了5次重连操作
- 由/proc/sys/net/ipv4/tcp_syn_retries内核变量所定义的。

### TCP状态转移

![[Pasted image 20240622195405.png]]

Linux为了防止孤儿连接长时间存留在内核中，定义了两个内核变量：
- /proc/sys/net/ipv4/tcp_max_orphans指定内核能接管的孤儿连接数目
- /proc/sys/net/ipv4/tcp_fin_timeout指定孤儿连接在内核中生存的时间
### 复位报文段

TCP连接的一端会向另一端发送携带RST标志的报文段，即复位报文段，以通知对方关闭连接或重新建立连接。
应用程序可以使用socket选项SO_LINGER来发送复位报文段，以异常终止一个连接。


### TCP交互数据流

Nagle算法要求一个TCP连接的通信双方在任意时刻都最多只能发送一个未被确认的TCP报文段，在该TCP报文段的确认到达之前不能发送其他TCP报文段。另一方面，发送方在等待确认的同时收集本端需要发送的微量数据，并在确认到来时以一个TCP报文段将它们全部发出。这样就极大地减少了网络上的微小TCP报文段的数量。

### 带外数据

带外（Out Of Band，OOB）数据的概念，用于迅速通告对方本端发生的重要事件。
带外数据的使用很少见，已知的仅有telnet、ftp等远程非活跃程序。

### TCP超时重传

TCP服务必须能够重传超时时间内未收到确认的TCP报文段。为此，TCP模块为每个TCP报文段都维护一个重传定时器，该定时器在TCP报文段第一次被发送时启动。如果超时时间内未收到接收方的应答，TCP模块将重传TCP报文段并重置定时器。至于下次重传的超时时间如何选择，以及最多执行多少次重传，就是TCP的重传策略。

/proc/sys/net/ipv4/tcp_retries1指定在底层IP接管之前TCP最少执行的重传次数，默认值是3。
/proc/sys/net/ipv4/tcp_retries2指定连接放弃前TCP最多可以执行的重传次数，默认值是15（一般对应13～30 min）。

### 拥塞控制

拥塞控制的四个部分：慢启动（slow start）、拥塞避免（congestion avoidance）、快速重传（fast retransmit）和快速恢复（fast recovery）。

拥塞控制算法在Linux下有多种实现，比如reno算法、vegas算法和cubic算法等。它们或者部分或者全部实现了上述四个部分。

/proc/sys/net/ipv4/tcp_congestion_control文件指示机器当前所使用的拥塞控制算法。