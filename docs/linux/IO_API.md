

### 　pipe函数

pipe函数可用于创建一个管道，以实现进程间通信。

```
#include＜unistd.h＞

int pipe(int fd[2]);
```

pipe函数的参数是一个包含两个int型整数的数组指针。该函数成功时返回0，并将一对打开的文件描述符值填入其参数指向的数组。如果失败，则返回-1并设置errno。

通过pipe函数创建的这两个文件描述符fd\[0]和fd\[1]分别构成管道的两端，往fd\[1]写入的数据可以从fd\[0]读出。并且，**fd\[0]只能用于从管道读出数据，fd\[1]则只能用于往管道写入数据**，而不能反过来使用。

socket的基础API中有一个socketpair函数。它能够方便地创建双向管道。

```
#include＜sys/types.h＞
#include＜sys/socket.h＞

int socketpair(int domain,int type,int protocol,int fd[2]);
```

socketpair前三个参数的含义与socket系统调用的三个参数完全相同，但domain只能使用UNIX本地域协议族AF_UNIX，因为我们仅能在本地使用这个双向管道。最后一个参数则和pipe系统调用的参数一样，只不过socketpair创建的这对文件描述符都是既可读又可写的。socketpair成功时返回0，失败时返回-1并设置errno。


### dup

把标准输入重定向到一个文件，或者把标准输出重定向到一个网络连接

```
#include＜unistd.h＞

int dup(int file_descriptor);

int dup2(int file_descriptor_one,int file_descriptor_two);

```

dup函数创建一个新的文件描述符，该新文件描述符和原有文件描述符file_descriptor指向相同的文件、管道或者网络连接。并且dup返回的文件描述符总是取系统当前可用的最小整数值。

dup2和dup类似，不过它将返回第一个不小于file_descriptor_two的整数值。dup和dup2系统调用失败时返回-1并设置errno。


[[test_cgi.cpp]]

先关闭标准输出文件描述符STDOUT_FILENO（其值是1），然后复制socket文件描述符connfd。因为dup总是返回系统中最小的可用文件描述符，所以它的返回值实际上是1，即之前关闭的标准输出文件描述符的值。这样一来，服务器输出到标准输出的内容（这里是“abcd”）就会直接发送到与客户连接对应的socket上，因此printf调用的输出将被客户端获得（而不是显示在服务器程序的终端上）。这就是CGI服务器的基本工作原理。



### readv函数和writev函数

readv函数将数据从文件描述符读到分散的内存块中，即分散读；
writev函数则将多块分散的内存数据一并写入文件描述符中，即集中写。

```
#include＜sys/uio.h＞

ssize_t readv(int fd,const struct iovec*vector,int count)；

ssize_t writev(int fd,const struct iovec*vector,int count);

struct iovec

{
void*iov_base;/*内存起始地址*/
size_t iov_len;/*这块内存的长度*/
};


```

fd参数是被操作的目标文件描述符。
vector参数的类型是iovec结构数组。
count参数是vector数组的长度，即有多少块内存数据需要从fd读出或写到fd。

readv和writev在成功时返回读出/写入fd的字节数，失败则返回-1并设置errno。
它们相当于简化版的recvmsg和sendmsg函数。


[[test_writev.cpp]]

直接将目标文件作为第3个参数传递给服务器程序，客户telnet到该服务器上即可获得该文件。


### sendfile

在两个文件描述符之间直接传递数据（完全在内核中操作），从而避免了内核缓冲区和用户缓冲区之间的数据拷贝，效率很高，这被称为零拷贝。

```

#include＜sys/sendfile.h＞

ssize_t sendfile(int out_fd,int in_fd,off_t*offset,size_t count);
```

in_fd参数是待读出内容的文件描述符，
out_fd参数是待写入内容的文件描述符。
offset参数指定从读入文件流的哪个位置开始读，如果为空，则使用读入文件流默认的起始位置
count参数指定在文件描述符in_fd和out_fd之间传输的字节数。
sendfile成功时返回传输的字节数，失败则返回-1并设置errno。


[[test_sendfile.cpp]]
将目标文件作为第3个参数传递给服务器程序，客户telnet到该服务器上即可获得该文件。


### mmap函数和munmap函数

mmap函数用于申请一段内存空间。我们可以将这段内存作为进程间通信的共享内存，也可以将文件直接映射到其中。

munmap函数则释放由mmap创建的这段内存空间。


```
#include＜sys/mman.h＞

void*mmap(void*start,size_t length,int prot,int flags,int fd,off_t offset);

int munmap(void*start,size_t length);
```

- start参数允许用户使用某个特定的地址作为这段内存的起始地址。
- 如果它被设置成NULL，则系统自动分配一个地址。
- length参数指定内存段的长度。
- port参数用来设置内存段的访问权限。
	❑PROT_READ，内存段可读。
	❑PROT_WRITE，内存段可写。
	❑PROT_EXEC，内存段可执行。
	❑PROT_NONE，内存段不能被访问。
- flags参数控制内存段内容被修改后程序的行为。
  ![[Pasted image 20240624203110.png]]

- fd参数是被映射文件对应的文件描述符。它一般通过open系统调用获得。
- offset参数设置从文件的何处开始映射

mmap函数成功时返回指向目标内存区域的指针，失败则返回MAP_FAILED（(void*)-1）并设置errno。

munmap函数成功时返回0，失败则返回-1并设置errno。

### 　splice函数

splice函数用于在两个文件描述符之间移动数据，也是零拷贝操作。

```
#include＜fcntl.h＞

ssize_t splice(int fd_in,loff_t*off_in,int fd_out,loff_t*off_out,size_t len,unsigned int flags);
```

fd_in参数是待输入数据的文件描述符。如果fd_in是一个管道文件描述符，那么off_in参数必须被设置为NULL。如果fd_in不是一个管道文件描述符（比如socket），那么off_in表示从输入数据流的何处开始读取数据。此时，若off_in被设置为NULL，则表示从输入数据流的当前偏移位置读入；若off_in不为NULL，则它将指出具体的偏移位置。

len参数指定移动数据的长度；

flags参数则控制数据如何移动
![[Pasted image 20240624203502.png]]

使用splice函数时，fd_in和fd_out必须至少有一个是管道文件描述符。

splice函数调用成功时返回移动字节的数量。它可能返回0，表示没有数据需要移动，这发生在从管道中读取数据（fd_in是管道文件描述符）而该管道没有被写入任何数据时。

splice函数失败时返回-1并设置errno。
![[Pasted image 20240624203604.png]]


[[test_splice.cpp]]

通过splice函数将客户端的内容读入到pipefd[1]中，然后再使用splice函数从pipefd[0]中读出该内容到客户端，从而实现了简单高效的回射服务。


### tee函数

tee函数在两个管道文件描述符之间复制数据，也是零拷贝操作。

```
#include＜fcntl.h＞

ssize_t tee(int fd_in,int fd_out,size_t len,unsigned int flags);
```

fd_in和fd_out必须都是管道文件描述符
tee函数成功时返回在两个文件描述符之间复制的数据数量（字节数）。
返回0表示没有复制任何数据。tee失败时返回-1并设置errno。

[[test_tee.cpp]]

同时输出数据到终端和文件的程序


### 　fcntl函数

fcntl函数，正如其名字（file control）描述的那样，提供了对文件描述符的各种控制操作。

```
#include＜fcntl.h＞

int fcntl(int fd,int cmd,…);

```

fd参数是被操作的文件描述符，
cmd参数指定执行何种类型的操作。

![[Pasted image 20240624205612.png]]![[Pasted image 20240624205705.png]]

在网络编程中，fcntl函数通常用来将一个文件描述符设置为非阻塞的

```
int setnonblocking(int fd)
{
	int old_option=fcntl(fd,F_GETFL);/*获取文件描述符旧的状态标志*/
	int new_option=old_option|O_NONBLOCK;/*设置非阻塞标志*/
	fcntl(fd,F_SETFL,new_option);
	return old_option;/*返回文件描述符旧的状态标志，以便*/
	/*日后恢复该状态标志*/
}

```