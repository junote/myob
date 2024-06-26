
### select

elect系统调用的用途是：在一段指定时间内，监听用户感兴趣的文件描述符上的可读、可写和异常等事件。

```
#include＜sys/select.h＞

int select(int nfds,fd_set*readfds,fd_set*writefds,fd_set*exceptfds,struct timeval*timeout);


FD_ZERO(fd_set*fdset);/*清除fdset的所有位*/
FD_SET(int fd,fd_set*fdset);/*设置fdset的位fd*/
FD_CLR(int fd,fd_set*fdset);/*清除fdset的位fd*/
int FD_ISSET(int fd,fd_set*fdset);/*测试fdset的位fd是否被设置*/

struct timeval
{
long tv_sec;/*秒数*/
long tv_usec;/*微秒数*/
};
```

- nfds参数指定被监听的文件描述符的总数。它通常被设置为select监听的所有文件描述符中的最大值加1，因为文件描述符是从0开始计数的。
- readfds、writefds和exceptfds参数分别指向可读、可写和异常等事件对应的文件描述符集合。
- timeout参数用来设置select函数的超时时间。

select给我们提供了一个微秒级的定时方式。如果给timeout变量的tv_sec成员和tv_usec成员都传递0，则select将立即返回。如果给timeout传递NULL，则select将一直阻塞，直到某个文件描述符就绪。

select成功时返回就绪（可读、可写和异常）文件描述符的总数。

在网络编程中，下列情况下socket可读：
❑socket内核接收缓存区中的字节数大于或等于其低水位标记SO_RCVLOWAT。此时我们可以无阻塞地读该socket，并且读操作返回的字节数大于0。
❑socket通信的对方关闭连接。此时对该socket的读操作将返回0。
❑监听socket上有新的连接请求。
❑socket上有未处理的错误。此时我们可以使用getsockopt来读取和清除该错误。

下列情况下socket可写
❑socket内核发送缓存区中的可用字节数大于或等于其低水位标记SO_SNDLOWAT。此时我们可以无阻塞地写该socket，并且写操作返回的字节数大于0。
❑socket的写操作被关闭。对写操作被关闭的socket执行写操作将触发一个SIGPIPE信号。
❑socket使用非阻塞connect连接成功或者失败（超时）之后。
❑socket上有未处理的错误。此时我们可以使用getsockopt来读取和清除该错误。

[[test_select.cpp]]



### poll

poll系统调用和select类似，也是在指定时间内轮询一定数量的文件描述符，以测试其中是否有就绪者。


```
#include＜poll.h＞

int poll(struct pollfd*fds,nfds_t nfds,int timeout);

struct pollfd
{
int fd;/*文件描述符*/
short events;/*注册的事件*/
short revents;/*实际发生的事件，由内核填充*/
};

typedef unsigned long int nfds_t;
```

fds参数是一个pollfd结构类型的数组，它指定所有我们感兴趣的文件描述符上发生的可读、可写和异常等事件。

fd成员指定文件描述符；events成员告诉poll监听fd上的哪些事件，它是一系列事件的按位或；revents成员则由内核修改，以通知应用程序fd上实际发生了哪些事件。
![[Pasted image 20240626194959.png]]
nfds参数指定被监听事件集合fds的大小。

timeout参数指定poll的超时值，单位是毫秒。当timeout为-1时，poll调用将永远阻塞，直到某个事件发生；当timeout为0时，poll调用将立即返回。

poll系统调用的返回值的含义与select相同。\


### epoll

epoll使用一组函数来完成任务，而不是单个函数。其次，epoll把用户关心的文件描述符上的事件放在内核里的一个事件表中，从而无须像select和poll那样每次调用都要重复传入文件描述符集或事件集。但epoll需要使用一个额外的文件描述符，来唯一标识内核中的这个事件表。

```
#include＜sys/epoll.h＞

int epoll_create(int size)

int epoll_ctl(int epfd,int op,int fd,struct epoll_event*event)

```

fd参数是要操作的文件描述符，op参数则指定操作类型。操作类型有如下3种：
❑EPOLL_CTL_ADD，往事件表中注册fd上的事件。
❑EPOLL_CTL_MOD，修改fd上的注册事件。
❑EPOLL_CTL_DEL，删除fd上的注册事件。


event参数指定事件，它是epoll_event结构指针类型。
其中events成员描述事件类型。**epoll支持的事件类型和poll基本相同**。表示epoll事件类型的宏是在poll对应的宏前加上“E”
```
struct epoll_event
{
__uint32_t events;/*epoll事件*/
epoll_data_t data;/*用户数据*/
};
```

data成员用于存储用户数据，其类型epoll_data_t的定义如下
其4个成员中使用最多的是fd，它指定事件所从属的目标文件描述符。ptr成员可用来指定与fd相关的用户数据。
```
typedef union epoll_data
{
void*ptr;
int fd;
uint32_t u32;
uint64_t u64;
}epoll_data_t;
```

epoll系列系统调用的主要接口是epoll_wait函数。它在一段超时时间内等待一组文件描述符上的事件
```
int epoll_wait(int epfd,struct epoll_event*events,int maxevents,int timeout);
```

- 该函数成功时返回就绪的文件描述符的个数，失败时返回-1并设置errno。
- epoll_wait函数如果检测到事件，就将所有就绪的事件从内核事件表（由epfd参数指定）中复制到它的第二个参数events指向的数组中。
- maxevents参数指定最多监听多少个事件，它必须大于0。
- timeout参数的含义与poll接口的timeout参数相同。


epoll对文件描述符的操作有两种模式：LT（Level Trigger，电平触发）模式和ET（Edge Trigger，边沿触发）模式。LT模式是默认的工作模式，这种模式下epoll相当于一个效率较高的poll。当往epoll内核事件表中注册一个文件描述符上的EPOLLET事件时，epoll将以ET模式来操作该文件描述符。ET模式是epoll的高效工作模式。


对于采用LT工作模式的文件描述符，当epoll_wait检测到其上有事件发生并将此事件通知应用程序后，应用程序可以不立即处理该事件。这样，当应用程序下一次调用epoll_wait时，epoll_wait还会再次向应用程序通告此事件，直到该事件被处理。而对于采用ET工作模式的文件描述符，当epoll_wait检测到其上有事件发生并将此事件通知应用程序后，应用程序必须立即处理该事件，因为后续的epoll_wait调用将不再向应用程序通知这一事件。可见，ET模式在很大程度上降低了同一个epoll事件被重复触发的次数，因此效率要比LT模式高。


[[test_epoll_et_lt.cpp]]


### 复用函数的比较

![[Pasted image 20240626211158.png]]