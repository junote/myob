
### API

#### 大端
字节序分为大端字节序（big endian）和小端字节序（little endian）。大端字节序是指一个整数的高位字节（23～31 bit）存储在内存的低地址处，低位字节（0～7 bit）存储在内存的高地址处。小端字节序则是指整数的高位字节存储在内存的高地址处，而低位字节则存储在内存的低地址处。

[[byteorder.c]]


在两台使用不同字节序的主机之间直接传递时，接收端必然错误地解释之。解决问题的方法是：发送端总是把要发送的数据转化成大端字节序数据后再发送，而接收端知道对方传送过来的数据总是采用大端字节序，所以接收端可以根据自身采用的字节序决定是否对接收到的数据进行转换(小端机转换，大端机不转换).

**大端字节序也称为网络字节序**，它给所有接收数据的主机提供了一个正确解释收到的格式化数据的保证。
Linux提供了如下4个函数来完成主机字节序和网络字节序之间的转换：

```
#include＜netinet/in.h＞
//host to network long
unsigned long int htonl(unsigned long int hostlong);
unsigned short int htons(unsigned short int hostshort);

unsigned long int ntohl(unsigned long int netlong);
unsigned short int ntohs(unsigned short int netshort);
```


#### socket地址

```
#include＜bits/socket.h＞

struct sockaddr
{
	sa_family_t sa_family;
	char sa_data[14];
}
```

- sa_family成员是地址族类型（sa_family_t）的变量。地址族类型通常与协议族类型对应。
  ![[Pasted image 20240623161757.png]]

- sa_data成员用于存放socket地址值。
  ![[Pasted image 20240623161838.png]]

- 14字节的sa_data根本无法完全容纳多数协议族的地址值。因此，Linux定义了下面这个新的通用socket地址结构体
```
#include＜bits/socket.h＞

struct sockaddr_storage
{
	sa_family_t sa_family;
	unsigned long int__ss_align;
	char__ss_padding[128-sizeof(__ss_align)];
}
```

- Linux为各个协议族提供了专门的socket地址结构体。
```
#include＜sys/un.h＞

struct sockaddr_un
{
	sa_family_t sin_family;/*地址族：AF_UNIX*/
	char sun_path[108];/*文件路径名*/
};

struct sockaddr_in
{
    sa_family_t sin_family; /*地址族：AF_INET*
    u_int16_t sin_port; /*端口号，要用网络字节序表示*/
    struct in_addr sin_addr; /*IPv4地址结构体，见下面*/
};
struct in_addr
{
    u_int32_t s_addr; /*IPv4地址，要用网络字节序表示*/
};

struct sockaddr_in6
{
    sa_family_t sin6_family; /*地址族：AF_INET6*/
    u_int16_t sin6_port; /*端口号，要用网络字节序表示*/
    u_int32_t sin6_flowinfo; /*流信息，应设置为0*/
    struct in6_addr sin6_addr; /*IPv6地址结构体，见下面*/
    u_int32_t sin6_scope_id; /*scope ID，尚处于实验阶段*/
};
struct in6_addr
{
    unsigned char sa_addr[16]; /*IPv6地址，要用网络字节序表示*/
};
```

#### IP地址转换函数

用点分十进制字符串表示IPv4地址，以及用十六进制字符串表示IPv6地址。
用于用点分十进制字符串表示的IPv4地址和用网络字节序整数表示的IPv4地址之间的转换
```
#include＜arpa/inet.h＞

in_addr_t inet_addr(const char*strptr);
int inet_aton(const char*cp,struct in_addr*inp);
char* inet_ntoa(struct in_addr in);

int inet_pton(int af,const char*src,void*dst);
const char*inet_ntop(int af,const void*src,char*dst,socklen_t cnt);

#define INET_ADDRSTRLEN 16
#define INET6_ADDRSTRLEN 46

```
- inet_addr函数将用点分十进制字符串表示的IPv4地址转化为用网络字节序整数表示的IPv4地址。它失败时返回INADDR_NONE。
- inet_aton函数完成和inet_addr同样的功能，但是将转化结果存储于参数inp指向的地址结构中。它成功时返回1，失败则返回0。
- inet_ntoa函数将用网络字节序整数表示的IPv4地址转化为用点分十进制字符串表示的IPv4地址。
- inet_pton函数将用字符串表示的IP地址src（用点分十进制字符串表示的IPv4地址或用十六进制字符串表示的IPv6地址）转换成用网络字节序整数表示的IP地址，并把转换结果存储于dst指向的内存中。
	- af参数指定地址族，可以是AF_INET或者AF_INET6。
	- inet_pton成功时返回1，失败则返回0并设置errno
- net_ntop函数进行相反的转换，前三个参数的含义与inet_pton的参数相同，最后一个参数cnt指定目标存储单元的大小。


### socket

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

int socket(int domain,int type,int protocol);
```

domain参数告诉系统使用哪个底层协议族。
	对TCP/IP协议族而言，该参数应该设置为PF_INET（Protocol Family of Internet，用于IPv4）或PF_INET6（用于IPv6）；
	对于UNIX本地域协议族而言，该参数应该设置为PF_UNIX。

type参数指定服务类型。服务类型主要有SOCK_STREAM服务（流服务）和SOCK_UGRAM（数据报）服务。
	对TCP/IP协议族而言，其值取SOCK_STREAM表示传输层使用TCP协议，取SOCK_DGRAM表示传输层使用UDP协议。
	
	type参数可以接受上述服务类型与下面两个重要的标志相与的值：SOCK_NONBLOCK和SOCK_CLOEXEC。它们分别表示将新创建的socket设为非阻塞的，以及用fork调用创建子进程时在子进程中关闭该socket。

protocol参数是在前两个参数构成的协议集合下，再选择一个具体的协议。不过这个值通常都是唯一的（前两个参数已经完全决定了它的值）。几乎在所有情况下，我们都应该把它设置为0，表示使用默认协议。

socket系统调用成功时返回一个socket文件描述符，失败则返回-1并设置errno。

### bind

在服务器程序中，我们通常要命名socket，因为只有命名后客户端才能知道该如何连接它。客户端则通常不需要命名socket，而是采用匿名方式，即使用操作系统自动分配的socket地址。命名socket的系统调用是bind，其定义如下：

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

int bind(int sockfd,const struct sockaddr*my_addr,socklen_t addrlen);

```

bind将my_addr所指的socket地址分配给未命名的sockfd文件描述符，addrlen参数指出该socket地址的长度。

bind成功时返回0，失败则返回-1并设置errno。其中两种常见的errno是EACCES和EADDRINUSE，
- EACCES，被绑定的地址是受保护的地址，仅超级用户能够访问。比如普通用户将socket绑定到知名服务端口（端口号为0～1023）上时，bind将返回EACCES错误。
- EADDRINUSE，被绑定的地址正在使用中。比如将socket绑定到一个处于TIME_WAIT状态的socket地址。

### listen

创建一个监听队列以存放待处理的客户连接

```
#include＜sys/socket.h＞

int listen(int sockfd,int backlog);
```

- sockfd参数指定被监听的socket。
- backlog参数提示内核监听队列的最大长度。
	监听队列的长度如果超过backlog，服务器将不受理新的客户连接，客户端也将收到ECONNREFUSED错误信息。
	
	backlog参数是指所有处于半连接状态（SYN_RCVD）和完全连接状态（ESTABLISHED）的socket的上限。
	
	处于半连接状态的socket的上限则由/proc/sys/net/ipv4/tcp_max_syn_backlog内核参数定义。
	
- listen成功时返回0，失败则返回-1并设置errno。

[[test_listen.cpp]]


### accept

从listen监听队列中接受一个连接

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

int accept(int sockfd,struct sockaddr*addr,socklen_t*addrlen);
```

- sockfd参数是执行过listen系统调用的监听socket
- addr参数用来获取被接受连接的远端socket地址，该socket地址的长度由addrlen参数指出。
- accept成功时返回一个新的连接socket，该socket唯一地标识了被接受的这个连接，服务器可通过读写该socket来与被接受连接对应的客户端通信。accept失败时返回-1并设置errno。

[[test_accept.cpp]]


### connect

客户端需要通过如下系统调用来主动与服务器建立连接

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

int connect(int sockfd,const struct sockaddr*serv_addr,socklen_t addrlen);

```

- sockfd参数由socket系统调用返回一个socket。
- serv_addr参数是服务器监听的socket地址，
- addrlen参数则指定这个地址的长度。
- connect成功时返回0。
- connect失败则返回-1并设置errno。
	- ECONNREFUSED，目标端口不存在，连接被拒绝。
	- ETIMEDOUT，连接超时。

### close

```
#include＜unistd.h＞

int close(int fd);
```

fd参数是待关闭的socket。不过，close系统调用并非总是立即关闭一个连接，而是将fd的引用计数减1。**只有当fd的引用计数为0时，才真正关闭连接**。

如果无论如何都要立即终止连接（而不是将socket的引用计数减1），可以使用如下的shutdown系统调用
```
#include＜sys/socket.h＞

int shutdown(int sockfd,int howto);
```

sockfd参数是待关闭的socket。howto参数决定了shutdown的行为

![[Pasted image 20240623172413.png]]

shutdown成功时返回0，失败则返回-1并设置errno。


### recv_send

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

ssize_t recv(int sockfd,void*buf,size_t len,int flags);

ssize_t send(int sockfd,const void*buf,size_t len,int flags);
```

- recv读取sockfd上的数据，buf和len参数分别指定读缓冲区的位置和大小，flags参数的含义见后文，通常设置为0即可。
- recv成功时返回实际读取到的数据的长度，它可能小于我们期望的长度len。因此我们可能要多次调用recv，才能读取到完整的数据。
- recv可能返回0，这意味着通信对方已经关闭连接了。recv出错时返回-1并设置errno。


- send往sockfd上写入数据，buf和len参数分别指定写缓冲区的位置和大小。
- send成功时返回实际写入的数据的长度，失败则返回-1并设置errno。

flags参数为数据收发提供了额外的控制

![[Pasted image 20240623172711.png]]

[[test_oob_recv.cpp]]  [[test_oob_send.cpp]]


### recvfrom_sendto

用于UDP数据报读写的系统调用

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

ssize_t recvfrom(int sockfd,void*buf,size_t len,int flags,struct sockaddr*src_addr,socklen_t *addrlen);

ssize_t sendto(int sockfd,const void*buf,size_t len,int flags,const struct sockaddr*dest_addr,socklen_t addrlen);
```


recvfrom读取sockfd上的数据，buf和len参数分别指定读缓冲区的位置和大小。因为UDP通信没有连接的概念，所以我们每次读取数据都需要获取发送端的socket地址，即参数src_addr所指的内容，addrlen参数则指定该地址的长度。


sendto往sockfd上写入数据，buf和len参数分别指定写缓冲区的位置和大小。dest_addr参数指定接收端的socket地址，addrlen参数则指定该地址的长度。

flags参数以及返回值的含义均与send/recv系统调用的flags参数及返回值相同。

recvfrom/sendto系统调用也可以用于面向连接（STREAM）的socket的数据读写，只需要把最后两个参数都设置为NULL以忽略发送端/接收端的socket地址（因为我们已经和对方建立了连接，所以已经知道其socket地址了）


### recvmg_sendmfg

socket编程接口还提供了一对通用的数据读写系统调用。它们不仅能用于TCP流数据，也能用于UDP数据报

```
#include＜sys/socket.h＞

ssize_t recvmsg(int sockfd,struct msghdr*msg,int flags);

ssize_t sendmsg(int sockfd,struct msghdr*msg,int flags);
```

sockfd参数指定被操作的目标socket。msg参数是msghdr结构体类型的指针，

```
struct msghdr

{

void *msg_name;/*socket地址*/

socklen_t msg_namelen;/*socket地址的长度*/

struct iovec*msg_iov;/*分散的内存块，见后文*/

int msg_iovlen;/*分散内存块的数量*/

void *msg_control;/*指向辅助数据的起始位置*/

socklen_t msg_controllen;/*辅助数据的大小*/

int msg_flags;/*复制函数中的flags参数，并在调用过程中更新*/

};
```

- msg_name成员指向一个socket地址结构变量。它指定通信对方的socket地址。对于面向连接的TCP协议，该成员没有意义，必须被设置为NULL。
- msg_namelen成员则指定了msg_name所指socket地址的长度。
- msg_iov成员是iovec结构体类型的指针,iovec结构体封装了一块内存的起始位置和长度。
```
struct iovec

{
void*iov_base;/*内存起始地址*/
size_t iov_len;/*这块内存的长度*/
};
```
- msg_iovlen指定这样的iovec结构对象有多少个。
- msg_control和msg_controllen成员用于辅助数据的传送。
- msg_flags成员无须设定，它会复制recvmsg/sendmsg的flags参数的内容以影响数据读写过程。


### 　带外标记

Linux内核检测到TCP紧急标志时，将通知应用程序有带外数据需要接收。内核通知应用程序带外数据到达的两种常见方式是：I/O复用产生的异常事件和SIGURG信号。

```
#include＜sys/socket.h＞

int sockatmark(int sockfd);
```

sockatmark判断sockfd是否处于带外标记，即下一个被读取到的数据是否是带外数据。
	如果是，sockatmark返回1，此时我们就可以利用带MSG_OOB标志的recv调用来接收带外数据。
	如果不是，则sockatmark返回0。

### 地址信息函数

```
#include＜sys/socket.h＞

int getsockname(int sockfd,struct sockaddr*address,socklen_t*address_len);

int getpeername(int sockfd,struct sockaddr*address,socklen_t*address_len);
```

getsockname获取sockfd对应的本端socket地址，并将其存储于address参数指定的内存中，该socket地址的长度则存储于address_len参数指向的变量中。如果实际socket地址的长度大于address所指内存区的大小，那么该socket地址将被截断。getsockname成功时返回0，失败返回-1并设置errno。

getpeername获取sockfd对应的远端socket地址，其参数及返回值的含义与getsockname的参数及返回值相同。


### 　socket选项

```
#include＜sys/socket.h＞

int getsockopt(int sockfd,int level,int
option_name,void*option_value,socklen_t*restrict option_len);

int setsockopt(int sockfd,int level,int option_name,const void*option_value,socklen_t option_len);
```

sockfd参数指定被操作的目标socket。level参数指定要操作哪个协议的选项（即属性），比如IPv4、IPv6、TCP等。option_name参数则指定选项的名字。

![[Pasted image 20240623200046.png]]


- 设置socket选项SO_REUSEADDR来强制使用被处于TIME_WAIT状态的连接占用的socket地址。
  `setsockopt(sock,SOL_SOCKET,SO_REUSEADDR,＆reuse,sizeof(reuse));`
- SO_RCVBUF和SO_SNDBUF选项分别表示TCP接收缓冲区和发送缓冲区的大小。
	- TCP接收缓冲区的最小值是256字节，而发送缓冲区的最小值是2048字节
	- /proc/sys/net/ipv4/tcp_rmem和/proc/sys/net/ipv4/tcp_wmem来强制TCP接收缓冲区和发送缓冲区的大小没有最小值限制。
	- `setsockopt(sock,SOL_SOCKET,SO_SNDBUF,＆sendbuf,sizeof(sendbuf));`
	- `setsockopt(sock,SOL_SOCKET,SO_RCVBUF,＆recvbuf,sizeof(recvbuf));`
	- `getsockopt(sock,SOL_SOCKET,SO_RCVBUF,＆recvbuf,(socklen_t*)＆len);`
- SO_LINGER选项用于控制close系统调用在关闭TCP连接时的行为。
	- linger类型的结构体
	- l_onoff等于0。此时SO_LINGER选项不起作用，close用默认行为来关闭socket。
	- l_onoff不为0，l_linger等于0。此时close系统调用立即返回，TCP模块将丢弃被关闭的socket对应的TCP发送缓冲区中残留的数据，同时给对方发送一个复位报文段
	- l_onoff不为0，l_linger大于0。此时close的行为取决于两个条件：一是被关闭的socket对应的TCP发送缓冲区中是否还有残留的数据；二是该socket是阻塞的，还是非阻塞的。对于阻塞的socket，close将等待一段长为l_linger的时间，直到TCP模块发送完所有残留数据并得到对方的确认。如果这段时间内TCP模块没有发送完残留数据并得到对方的确认，那么close系统调用将返回-1并设置errno为EWOULDBLOCK。如果socket是非阻塞的，close将立即返回，此时我们需要根据其返回值和errno来判断残留数据是否已经发送完毕。
```
#include＜sys/socket.h＞

struct linger
{
int l_onoff;/*开启（非0）还是关闭（0）该选项*/
int l_linger;/*滞留时间*/
};
```


### 　网络信息API

#### getservbyname和getservbyport

getservbyname函数根据名称获取某个服务的完整信息，
getservbyport函数根据端口号获取某个服务的完整信息。

```
#include＜netdb.h＞

struct servent* getservbyname(const char*name,const char*proto);

struct servent* getservbyport(int port,const char*proto);


struct servent
{
char*s_name;/*服务名称*/
char**s_aliases;/*服务的别名列表，可能有多个*/
int s_port;/*端口号*/
char*s_proto;/*服务类型,通常是tcp或者udp*/
};
```

- name参数指定目标服务的名字，
- port参数指定目标服务对应的端口号。
- proto参数指定服务类型，给它传递“tcp”表示获取流服务，给它传递“udp”表示获取数据报服务，给它传递NULL则表示获取所有类型的服务。

#### getaddrinfo

getaddrinfo函数既能通过主机名获得IP地址（内部使用的是gethostbyname函数），也能通过服务名获得端口号（内部使用的是getservbyname函数）。


```
#include＜netdb.h＞

int getaddrinfo(const char*hostname,const char*service,const struct addrinfo*hints,struct addrinfo**result);

void freeaddrinfo(struct addrinfo*res);

struct addrinfo
{
int ai_flags;/*具体的网络协议*/
int ai_family;/*地址族*/
int ai_socktype;/*服务类型，SOCK_STREAM或SOCK_DGRAM*/
int ai_protocol;/*见后文*/
socklen_t ai_addrlen;/*socket地址ai_addr的长度*/
char*ai_canonname;/*主机的别名*/
struct sockaddr*ai_addr;/*指向socket地址*/
struct addrinfo*ai_next;/*指向下一个sockinfo结构的对象*/
};
```

- hostname参数可以接收主机名，也可以接收字符串表示的IP地址
- service参数可以接收服务名，也可以接收字符串表示的十进制端口号。
- hints参数是应用程序给getaddrinfo的一个提示，以对getaddrinfo的输出进行更精确的控制。hints参数可以被设置为NULL，表示允许getaddrinfo反馈任何可用的结果。
- result参数指向一个链表，该链表用于存储getaddrinfo反馈的结果。
- getaddrinfo反馈的每一条结果都是addrinfo结构体类型的对象
- ai_flags成员可以取表5-6中的标志的按位或
  ![[Pasted image 20240623202233.png]]

#### 　getnameinfo

getnameinfo函数能通过socket地址同时获得以字符串表示的主机名（内部使用的是gethostbyaddr函数）和服务名（内部使用的是getservbyport函数）。

```
#include＜netdb.h＞

int getnameinfo(const struct sockaddr*sockaddr,socklen_t addrlen,char*host,socklen_t hostlen,char*serv,socklen_t servlen,int flags);
```

主机名存储在host参数指向的缓存中，将服务名存储在serv参数指向的缓存中，hostlen和servlen参数分别指定这两块缓存的长度。

flags参数控制getnameinfo的行为
![[Pasted image 20240623202452.png]]


