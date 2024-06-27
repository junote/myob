
### fork

```
#include＜sys/types.h＞
#include＜unistd.h＞

pid_t fork(void);
```

该函数的每次调用都返回两次，在父进程中返回的是子进程的PID，在子进程中则返回0。

该返回值是后续代码判断当前进程是父进程还是子进程的依据。

fork调用失败时返回-1，并设置errno。

fork函数复制当前进程，在内核进程表中创建一个新的进程表项。新的进程表项有很多属性和原进程相同，比如堆指针、栈指针和标志寄存器的值。但也有许多属性被赋予了新的值，比如该进程的PPID被设置成原进程的PID，信号位图被清除

子进程的代码与父进程完全相同，同时它还会复制父进程的数据（堆数据、栈数据和静态数据）。数据的复制采用的是所谓的写时复制（copy on writte），即只有在任一进程（父进程或子进程）对数据执行了写操作时，复制才会发生（先是缺页中断，然后操作系统给子进程分配内存并复制父进程的数据）。\


### exec

在子进程中执行其他程序，即替换当前进程映像

```
#include＜unistd.h＞

extern char**environ;
int execl(const char*path,const char*arg,...);
int execlp(const char*file,const char*arg,...);
int execle(const char*path,const char*arg,...,char*const envp[]);
int execv(const char*path,char*const argv[]);
int execvp(const char*file,char*const argv[]);
int execve(const char*path,char*const argv[],char*const envp[]);

```

path参数指定可执行文件的完整路径，
file参数可以接受文件名，该文件的具体位置则在环境变量PATH中搜寻。
arg接受可变参数，
argv则接受参数数组，它们都会被传递给新程序（path或file指定的程序）的main函数。
envp参数用于设置新程序的环境变量。如果未设置它，则新程序将使用由全局变量environ指定的环境变量。

exec函数是不返回的，除非出错。它出错时返回-1，并设置errno。如果没出错，则原程序中exec调用之后的代码都不会执行，因为此时原程序已经被exec的参数指定的程序完全替换

exec函数不会关闭原程序打开的文件描述符，除非该文件描述符被设置了类似SOCK_CLOEXEC的属性


### 处理僵尸进程

函数在父进程中调用，以等待子进程的结束，并获取子进程的返回信息，从而避免了僵尸进程的产生，或者使子进程的僵尸态立即结束

```
#include＜sys/types.h＞
#include＜sys/wait.h＞

pid_t wait(int*stat_loc);

pid_t waitpid(pid_t pid,int*stat_loc,int options);
```

wait函数将阻塞进程，直到该进程的某个子进程结束运行为止。
它返回结束运行的子进程的PID，并将该子进程的退出状态信息存储于stat_loc参数指向的内存中。sys/wait.h头文件中定义了几个宏来帮助解释子进程的退出状态信息

![[Pasted image 20240627200219.png]]


**waitpid**只等待由pid参数指定的子进程。如果pid取值为-1，那么它就和wait函数相同，即等待任意一个子进程结束。
当options的取值是WNOHANG时，waitpid调用将是非阻塞的指定的目标子进程还没有结束或意外终止，则waitpid立即返回0；如果目标子进程确实正常退出了，则waitpid返回该子进程的PID。waitpid调用失败时返回-1并设置errno。

**当一个进程结束时，它将给其父进程发送一个SIGCHLD信号。**
在父进程中捕获SIGCHLD信号，并在信号处理函数中调用waitpid函数以“彻底结束”一个子进程
```
static void handle_child(int sig)
{
	pid_t pid;
	int stat;
	while((pid=waitpid(-1,＆stat,WNOHANG))＞0)
	{
		/*对结束的子进程进行善后处理*/
	}
}
```

### pipe

管道能在父、子进程间传递数据，利用的是fork调用之后两个管道文件描述符（fd[0]和fd[1]）都保持打开。

### 信号量

信号量（Semaphore）是一种特殊的变量，它只能取自然数值并且只支持两种操作：等待（wait）和信号（signal）。

对信号量的这两种操作更常用的称呼是P、V操作。这两个字母来自于荷兰语单词passeren（传递，就好像进入临界区）和vrijgeven（释放，就好像退出临界区）。假设有信号量SV，则对它的P、V操作含义如下：

❑P(SV)，如果SV的值大于0，就将它减1；如果SV的值为0，则挂起进程的执行。
❑V(SV)，如果有其他进程因为等待SV而挂起，则唤醒之；如果没有，则将SV加1。


Linux信号量的API都定义在sys/sem.h头文件中，主要包含3个系统调用：semget、semop和semctl。

```
#include＜sys/sem.h＞

int semget(key_t key,int num_sems,int sem_flags);
```

semget系统调用创建一个新的信号量集，或者获取一个已经存在的信号量集。

key参数是一个键值，用来标识一个全局唯一的信号量集，就像文件名全局唯一地标识一个文件一样。要通过信号量通信的进程需要使用相同的键值来创建/获取该信号量。

图　13-2　使用信号量保护关键代码段
在图13-2中，当关键代码段可用时，二进制信号量SV的值为1，进程A和B都有机会进入关键代码段。如果此时进程A执行了P(SV)操作将SV减1，则进程B若再执行P(SV)操作就会被挂起。直到进程A离开关键代码段，并执行V(SV)操作将SV加1，关键代码段才重新变得可用。如果此时进程B因为等待SV而处于挂起状态，则它将被唤醒，并进入关键代码段。同样，这时进程A如果再执行P(SV)操作，则也只能被操作系统挂起以等待进程B退出关键代码段。

注意　使用一个普通变量来模拟二进制信号量是行不通的，因为所有高级语言都没有一个原子操作可以同时完成如下两步操作：检测变量是否为true/false，如果是则再将它设置为false/true。

Linux信号量的API都定义在sys/sem.h头文件中，主要包含3个系统调用：semget、semop和semctl。它们都被设计为操作一组信号量，即信号量集，而不是单个信号量，因此这些接口看上去多少比我们期望的要复杂一点。我们将分3小节依次讨论之。

13.5.2　semget系统调用
semget系统调用创建一个新的信号量集，或者获取一个已经存在的信号量集。其定义如下：

#include＜sys/sem.h＞

int semget(key_t key,int num_sems,int sem_flags);

key参数是一个键值，用来标识一个全局唯一的信号量集，就像文件名全局唯一地标识一个文件一样。要通过信号量通信的进程需要使用相同的键值来创建/获取该信号量。

num_sems参数指定要创建/获取的信号量集中信号量的数目。如果是创建信号量，则该值必须被指定；如果是获取已经存在的信号量，则可以把它设置为0。

sem_flags参数指定一组标志。它低端的9个比特是该信号量的权限，其格式和含义都与系统调用open的mode参数相同。此外，它还可以和IPC_CREAT标志做按位“或”运算以创建新的信号量集。此时即使信号量已经存在，semget也不会产生错误。

semget成功时返回一个正整数值，它是信号量集的标识符；semget失败时返回-1，并设置errno。


```c
unsigned short semval;
/*信号量的值*/

unsigned short semzcnt;
/*等待信号量值变为0的进程数量*/

unsigned short semncnt;
/*等待信号量值增加的进程数量*/

pid_t sempid;
/*最后一次执行semop操作的进程ID*/

int semop(int sem_id,struct sembuf*sem_ops,size_t num_sem_ops);
```

sem_id参数是由semget调用返回的信号量集标识符，用以指定被操作的目标信号量集。

sem_ops参数指向一个sembuf结构体类型的数组，sembuf结构体的定义
```c
struct sembuf{
	unsigned short int sem_num;
	short int sem_op;
	short int sem_flg;
}
```

sem_num成员是信号量集中信号量的编号，0表示信号量集中的第一个信号量。
sem_op成员指定操作类型，其可选值为正整数、0和负整数。
sem_flg的可选值是IPC_NOWAIT和SEM_UNDO。
- IPC_NOWAIT的含义是，无论信号量操作是否成功，semop调用都将立即返回
-  SEM_UNDO的含义是，当进程退出时取消正在进行的semop操作。

- 如果sem_op大于0，则semop将被操作的信号量的值semval增加sem_op。该操作要求调用进程对被操作信号量集拥有写权限。
- 如果sem_op等于0，则表示这是一个“等待0”（wait-for-zero）操作。该操作要求调用进程对被操作信号量集拥有读权限。
- 如果sem_op小于0，则表示对信号量值进行减操作，即期望获得信号量。该操作要求调用进程对被操作信号量集拥有写权限。


semop成功时返回0，失败则返回-1并设置errno。失败的时候，sem_ops数组中指定的所有操作都不被执行。


```
#include＜sys/sem.h＞

int semctl(int sem_id,int sem_num,int command,...);
```

semctl系统调用允许调用者对信号量进行直接控制。

sem_id参数是由semget调用返回的信号量集标识符，用以指定被操作的信号量集
sem_num参数指定被操作的信号量在信号量集中的编号。
command参数指定要执行的命令。有的命令需要调用者传递第4个参数。第4个参数的类型由用户自己定义，但sys/sem.h头文件给出了它的推荐格式
```c
union semun
{
	int val;/*用于SETVAL命令*/
	struct semid_ds*buf;/*用于IPC_STAT和IPC_SET命令*/
	unsigned short*array;/*用于GETALL和SETALL命令*/
	struct seminfo*__buf;/*用于IPC_INFO命令*/
};

struct seminfo
{
	int semmap;/*Linux内核没有使用*/
	int semmni;/*系统最多可以拥有的信号量集数目*/
	int semmns;/*系统最多可以拥有的信号量数目*/
	int semmnu;/*Linux内核没有使用*/
	int semmsl;/*一个信号量集最多允许包含的信号量数目*/
	int semopm;/*semop一次最多能执行的sem_op操作数目*/
	int semume;/*Linux内核没有使用*/
	int semusz;/*sem_undo结构体的大小*/
	int semvmx;/*最大允许的信号量值*/
	/*最多允许的UNDO次数（带SEM_UNDO标志的semop操作的次数）*/
	int semaem;
};
```

semctl支持的所有命令
![[Pasted image 20240627202504.png]]GETNCNT、GETPID、GETVAL、GETZCNT和SETVAL操作的是单个信号量，它是由标识符sem_id指定的信号量集中的第sem_num个信号量；而其他操作针对的是整个信号量集，此时semctl的参数sem_num被忽略。


### 　共享内存

共享内存是最高效的IPC机制，因为它不涉及进程之间的任何数据传输。

#### 　shmget系统调用
shmget系统调用创建一段新的共享内存，或者获取一段已经存在的共享内存。

```c
#include＜sys/shm.h＞

int shmget(key_t key,size_t size,int shmflg);
```
key参数是一个键值，用来标识一段全局唯一的共享内存。
size参数指定共享内存的大小，单位是字节。如果是创建新的共享内存，则size值必须被指定。如果是获取已经存在的共享内存，则可以把size设置为0。
hmflg参数的使用和含义与semget系统调用的sem_flags参数相同。
❑SHM_HUGETLB，类似于mmap的MAP_HUGETLB标志，系统将使用“大页面”来为共享内存分配空间。
❑SHM_NORESERVE，类似于mmap的MAP_NORESERVE标志，不为共享内存保留交换分区（swap空间）。这样，当物理内存不足的时候，对该共享内存执行写操作将触发SIGSEGV信号。

shmget成功时返回一个正整数值，它是共享内存的标识符。shmget失败时返回-1，并设置errno。


#### shmat和shmdt系统调用

共享内存被创建/获取之后，我们不能立即访问它，而是需要先将它关联到进程的地址空间中。使用完共享内存之后，我们也需要将它从进程地址空间中分离。

```c
#include＜sys/shm.h＞

void*shmat(int shm_id,const void*shm_addr,int shmflg);

int shmdt(const void*shm_addr);
```

shm_id参数是由shmget调用返回的共享内存标识符。
shm_addr参数指定将共享内存关联到进程的哪块地址空间
shmflg参数的可选标志SHM_RND的影响：
❑如果shm_addr为NULL，则被关联的地址由操作系统选择。这是推荐的做法，以确保代码的可移植性。
❑如果shm_addr非空，并且SHM_RND标志未被设置，则共享内存被关联到addr指定的地址处。
❑如果shm_addr非空，并且设置了SHM_RND标志，则被关联的地址是\[shm_addr-(shm_addr%SHMLBA)]。SHMLBA的含义是“段低端边界地址倍数”（Segment Low Boundary Address Multiple），它必须是内存页面大小（PAGE_SIZE）的整数倍。
❑SHM_RDONLY。进程仅能读取共享内存中的内容。若没有指定该标志，则进程可同时对共享内存进行读写操作（当然，这需要在创建共享内存的时候指定其读写权限）。
❑SHM_REMAP。如果地址shmaddr已经被关联到一段共享内存上，则重新关联。
❑SHM_EXEC。它指定对共享内存段的执行权限。对共享内存而言，执行权限实际上和读权限是一样的。

shmat成功时返回共享内存被关联到的地址，失败则返回(void*)-1并设置errno。shmat成功时，将修改内核数据结构shmid_ds的部分字段，如下：
❑将shm_nattach加1。
❑将shm_lpid设置为调用进程的PID。
❑将shm_atime设置为当前的时间。

shmdt函数将关联到shm_addr处的共享内存从进程中分离。它成功时返回0，失败则返回-1并设置errno。shmdt在成功调用时将修改内核数据结构shmid_ds的部分字段，如下：
❑将shm_nattach减1。
❑将shm_lpid设置为调用进程的PID。
❑将shm_dtime设置为当前的时间。


#### shmctl系统调用

```c
#include＜sys/shm.h＞

int shmctl(int shm_id,int command,struct shmid_ds*buf);
```
shm_id参数是由shmget调用返回的共享内存标识符。
command参数指定要执行的命令。
shmctl支持的所有命令如表

![[Pasted image 20240627204358.png]]

#### 　共享内存的POSIX方法

```c
#include＜sys/mman.h＞
#include＜sys/stat.h＞
#include＜fcntl.h＞

int shm_open(const char*name,int oflag,mode_t mode);
```
shm_open的使用方法与open系统调用完全相同。
name参数指定要创建/打开的共享内存对象。
oflag参数指定创建方式。
❑O_RDONLY。以只读方式打开共享内存对象。
❑O_RDWR。以可读、可写方式打开共享内存对象。
❑O_CREAT。如果共享内存对象不存在，则创建之。此时mode参数的最低9位将指定该共享内存对象的访问权限。共享内存对象被创建的时候，其初始长度为0。
❑O_EXCL。和O_CREAT一起使用，如果由name指定的共享内存对象已经存在，则shm_open调用返回错误，否则就创建一个新的共享内存对象。
❑O_TRUNC。如果共享内存对象已经存在，则把它截断，使其长度为0。

shm_open调用成功时返回一个文件描述符。该文件描述符可用于后续的mmap调用，从而将共享内存关联到调用进程。shm_open失败时返回-1，并设置errno。


```c
int shm_unlink(const char*name);
```
该函数将name参数指定的共享内存对象标记为等待删除。当所有使用该共享内存对象的进程都使用ummap将它从进程中分离之后，系统将销毁这个共享内存对象所占据的资源。

[[test_shm.cpp]]

### 　消息队列

消息队列是在两个进程之间传递二进制块数据的一种简单有效的方式。每个数据块都有一个特定的类型，接收方可以根据类型来有选择地接收数据，而不一定像管道和命名管道那样必须以先进先出的方式接收数据。


#### 　msgget系统调用

```c
#include＜sys/msg.h＞

int msgget(key_t key,int msgflg);


```

key参数是一个键值，用来标识一个全局唯一的消息队列。
msgflg参数使用和含义与semget系统调用的sem_flags参数相同。

msgget成功时返回一个正整数值，它是消息队列的标识符。msgget失败时返回-1，并设置errno。

如果msgget用于创建消息队列，则与之关联的内核数据结构msqid_ds将被创建并初始化。msqid_ds结构体的定义
```c
struct msqid_ds

{
	struct ipc_perm msg_perm;/*消息队列的操作权限*/
	time_t msg_stime;/*最后一次调用msgsnd的时间*/
	time_t msg_rtime;/*最后一次调用msgrcv的时间*/
	time_t msg_ctime;/*最后一次被修改的时间*/
	unsigned long__msg_cbytes;/*消息队列中已有的字节数*/
	msgqnum_t msg_qnum;/*消息队列中已有的消息数*/
	msglen_t msg_qbytes;/*消息队列允许的最大字节数*/
	pid_t msg_lspid;/*最后执行msgsnd的进程的PID*/
	pid_t msg_lrpid;/*最后执行msgrcv的进程的PID*/
};

```

#### 　msgsnd系统调用

msgsnd系统调用把一条消息添加到消息队列中。
```c
#include＜sys/msg.h＞

int msgsnd(int msqid,const void*msg_ptr,size_t msg_sz,int msgflg);

struct msgbuf
{
	long mtype;/*消息类型*/
	char mtext[512];/*消息数据*/
};
```

msqid参数是由msgget调用返回的消息队列标识符。
msg_ptr参数指向一个准备发送的消息
	mtype成员指定消息的类型，它必须是一个正整数。mtext是消息数据。
msg_sz参数是消息的数据部分（mtext）的长度。这个长度可以为0，表示没有消息数据。
msgflg参数控制msgsnd的行为。它通常仅支持IPC_NOWAIT标志，即以非阻塞的方式发送消息。默认情况下，发送消息时如果消息队列满了，则msgsnd将阻塞。若IPC_NOWAIT标志被指定，则msgsnd将立即返回并设置errno为EAGAIN。


#### 　msgrcv系统调用

msgrcv系统调用从消息队列中获取消息。

```c
int msgrcv(int msqid,void*msg_ptr,size_t msg_sz,long int msgtype,int msgflg);
```

msqid参数是由msgget调用返回的消息队列标识符。
msg_ptr参数用于存储接收的消息，msg_sz参数指的是消息数据部分的长度。
msgtype参数指定接收何种类型的消息。我们可以使用如下几种方式来指定消息类型：
❑msgtype等于0。读取消息队列中的第一个消息。
❑msgtype大于0。读取消息队列中第一个类型为msgtype的消息（除非指定了标志MSG_EXCEPT，见后文）。
❑msgtype小于0。读取消息队列中第一个类型值比msgtype的绝对值小的消息。

参数msgflg控制msgrcv函数的行为。它可以是如下一些标志的按位或：
❑IPC_NOWAIT。如果消息队列中没有消息，则msgrcv调用立即返回并设置errno为ENOMSG。
❑MSG_EXCEPT。如果msgtype大于0，则接收消息队列中第一个非msgtype类型的消息。
❑MSG_NOERROR。如果消息数据部分的长度超过了msg_sz，就将它截断。

处于阻塞状态的msgrcv调用还可能被如下两种异常情况所中断：
❑消息队列被移除。此时msgrcv调用将立即返回并设置errno为EIDRM。
❑程序接收到信号。此时msgrcv调用将立即返回并设置errno为EINTR。
msgrcv成功时返回0，失败则返回-1并设置errno。msgrcv成功时将修改内核数据结构msqid_ds的部分字段，如下所示：
	❑将msg_qnum减1。
	❑将msg_lrpid设置为调用进程的PID。
	❑将msg_rtime设置为当前的时间。

#### 　msgctl系统调用

msgctl系统调用控制消息队列的某些属性。

```c
#include＜sys/msg.h＞

int msgctl(int msqid,int command,struct msqid_ds*buf);
```

msqid参数是由msgget调用返回的共享内存标识符。
command参数指定要执行的命令。
![[Pasted image 20240627211553.png]]

msgctl成功时的返回值取决于command参数, msgctl函数失败时返回-1并设置errno。


### socket传递

可以利用UNIX域socket在进程间传递特殊的辅助数据，以实现文件描述符的传递

[[test_socket_share_process.cpp]]