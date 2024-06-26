
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

```

