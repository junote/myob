
### kill

```
#include＜sys/types.h＞
#include＜signal.h＞

int kill(pid_t pid,int sig);
```

该函数把信号sig发送给目标进程；目标进程由pid参数指定
![[Pasted image 20240626214518.png]]
该函数成功时返回0，失败则返回-1并设置errno。
![[Pasted image 20240626214541.png]]

#### 　信号处理方式

目标进程在收到信号时，需要定义一个接收函数来处理之。

```
#include＜signal.h＞

typedef void(*__sighandler_t)(int);


#include＜bits/signum.h＞

#define SIG_DFL((__sighandler_t)0)
#define SIG_IGN((__sighandler_t)1)
```

信号处理函数只带有一个整型参数，该参数用来指示信号类型。
SIG_IGN表示忽略目标信号，SIG_DFL表示使用信号的默认处理方式。信号的默认处理方式有如下几种：结束进程（Term）、忽略信号（Ign）、结束进程并生成核心转储文件（Core）、暂停进程（Stop），以及继续进程（Cont）。



### Linux信号

![[Pasted image 20240626215244.png]]


### 　信号函数

```
#include＜signal.h＞

_sighandler_t signal(intsig,_sighandler_t_handler)
```

sig参数指出要捕获的信号类型。\_handler参数是_sighandler_t类型的函数指针，用于指定信号sig的处理函数。

signal函数成功时返回一个函数指针，该函数指针的类型也是_sighandler_t。这个返回值是前一次调用signal函数时传入的函数指针，或者是信号sig对应的默认处理函数指针SIG_DEF（如果是第一次调用signal的话）。

signal系统调用出错时返回SIG_ERR，并设置errno。

```
#include＜signal.h＞

int sigaction(int sig,const struct sigaction*act,struct sigaction*oact);
```

sig参数指出要捕获的信号类型，act参数指定新的信号处理方式，oact参数则输出信号先前的处理方式

```
struct sigaction
{
#ifdef__USE_POSIX199309
	union
	{
		_sighandler_t sa_handler;
		void(*sa_sigaction)(int,siginfo_t*,void*);
	}
	_sigaction_handler;
	#define sa_handler__sigaction_handler.sa_handler
	#define sa_sigaction__sigaction_handler.sa_sigaction
#else
	_sighandler_t sa_handler;
#endif
	_sigset_t sa_mask;
	int sa_flags;
	void(*sa_restorer)(void);
};
```

sa_hander成员指定信号处理函数。
sa_mask成员设置进程的信号掩码
sa_flags成员用于设置程序收到信号时的行为
![[Pasted image 20240626220812.png]]
sa_restorer成员已经过时，最好不要使用。
sigaction成功时返回0，失败则返回-1并设置errno。


### 　信号集

Linux使用数据结构sigset_t来表示一组信号

```
#include＜bits/sigset.h＞

#define_SIGSET_NWORDS(1024/(8*sizeof(unsigned long int)))
typedef struct
{
	unsigned long int__val[_SIGSET_NWORDS];
}__sigset_t;

int sigemptyset(sigset_t*_set)/*清空信号集*/
int sigfillset(sigset_t*_set)/*在信号集中设置所有信号*/
int sigaddset(sigset_t*_set,int_signo)/*将信号_signo添加至信号集中*/
int sigdelset(sigset_t*_set,int_signo)/*将信号_signo从信号集中删除*/
int sigismember(_const sigset_t*_set,int_signo)/*测试_signo是否在信号集中*/

int sigprocmask(int_how,_const sigset_t*_set,sigset_t*_oset);
```

sigset_t实际上是一个长整型数组，数组的每个元素的每个位表示一个信号。这种定义方式和文件描述符集fd_set类似。

Linux提供了一组函数来设置、修改、删除和查询信号集

\_set参数指定新的信号掩码，_oset参数则输出原来的信号掩码（如果不为NULL的话）。如果_set参数不为NULL，则_how参数指定设置进程信号掩码的方式，其可选值如表
![[Pasted image 20240626221803.png]]

设置进程信号掩码后，被屏蔽的信号将不能被进程接收。如果给进程发送一个被屏蔽的信号，则操作系统将该信号设置为进程的一个被挂起的信号。如果我们取消对被挂起信号的屏蔽，则它能立即被进程接收到。如下函数可以获得进程当前被挂起的信号集

```
int sigpending(sigset_t*set);
```

set参数用于保存被挂起的信号集。
igpending成功时返回0，失败时返回-1并设置errno。

[[test_signal.cpp]]