
### syslog

Linux提供一个守护进程来处理系统日志——syslogd，不过现在的Linux系统上使用的都是它的升级版——rsyslogd。

用户进程是通过调用syslog函数生成系统日志的。该函数将日志输出到一个UNIX本地域socket类型（AF_UNIX）的文件/dev/log中，rsyslogd则监听该文件以获取用户进程的输出。

内核日志由printk等函数打印至内核的环状缓存（ring buffer）中。环状缓存的内容直接映射到/proc/kmsg文件中。rsyslogd则通过读取该文件获得内核日志。

默认情况下，调试信息会保存至/var/log/debug文件，普通信息保存至/var/log/messages文件，内核消息则保存至/var/log/kern.log文件。

rsyslogd的主配置文件是/etc/rsyslog.conf，其中主要可以设置的项包括：内核日志输入路径，是否接收UDP日志及其监听端口（默认是514，见/etc/services文件），是否接收TCP日志及其监听端口，日志文件的权限，包含哪些子配置文件（比如/etc/rsyslog.d/*.conf）。rsyslogd的子配置文件则指定各类日志的目标存储文件。


```
#include＜syslog.h＞

void syslog(int priority,const char*message,...);

#define LOG_EMERG 0/*系统不可用*/
#define LOG_ALERT 1/*报警，需要立即采取动作*/
#define LOG_CRIT 2/*非常严重的情况*/
#define LOG_ERR 3/*错误*/
#define LOG_WARNING 4/*警告*/
#define LOG_NOTICE 5/*通知*/
#define LOG_INFO 6/*信息*/
#define LOG_DEBUG 7/*调试*/

```
该函数采用可变参数（第二个参数message和第三个参数…）来结构化输出。priority参数是所谓的设施值与日志级别的按位或。设施值的默认值是LOG_USER


```
void openlog(const char*ident,int logopt,int facility);

#define LOG_PID 0x01/*在日志消息中包含程序PID*/
#define LOG_CONS 0x02/*如果消息不能记录到日志文件，则打印至终端*/
#define LOG_ODELAY 0x04/*延迟打开日志功能直到第一次调用syslog*/
#define LOG_NDELAY 0x08/*不延迟打开日志功能*/

```

ident参数指定的字符串将被添加到日志消息的日期和时间之后，它通常被设置为程序的名字。logopt参数对后续syslog调用的行为进行配置

facility参数可用来修改syslog函数中的默认设施值。

```
int setlogmask(int maskpri);
```

maskpri参数指定日志掩码值。该函数始终会成功，它返回调用进程先前的日志掩码值。

```
void closelog();
```

关闭日志功能



### 　用户信息

下面这一组函数可以获取和设置当前进程的真实用户ID（UID）、有效用户ID（EUID）、真实组ID（GID）和有效组ID（EGID）：

```
#include＜sys/types.h>
#include＜unistd.h＞

uid_t getuid();/*获取真实用户ID*/
uid_t geteuid();/*获取有效用户ID*/

gid_t getgid();/*获取真实组ID*/
gid_t getegid();/*获取有效组ID*/

int setuid(uid_t uid);/*设置真实用户ID*/
int seteuid(uid_t uid);/*设置有效用户ID*/

int setgid(gid_t gid);/*设置真实组ID*/
int setegid(gid_t gid);/*设置有效组ID*/
```


### 　进程间关系

- `pid` – the process id.
- `ppid` – the `PID` of the parent of this process.
- `pgrp` – the process group id of the process.
- `sid` – the session id of the process.
- `tty` – the controlling terminal of the process. (The minor device number is contained in the combination of bits 31 to 20 and 7 to 0; the major device number is in bits 15 to 8.)
- `tgid` – the id of the foreground process group of the controlling terminal of the process.


Linux下每个进程都隶属于一个进程组，因此它们除了PID信息外，还有进程组ID（PGID）。

```
#include＜unistd.h＞

pid_t getpgid(pid_t pid);
```

该函数成功时返回进程pid所属进程组的PGID，失败则返回-1并设置errno。

每个进程组都有一个首领进程，其PGID和PID相同。进程组将一直存在，直到其中所有进程都退出，或者加入到其他进程组。

下面的函数用于设置PGID：
```
#include＜unistd.h＞

int setpgid(pid_t pid,pid_t pgid);
```

该函数将PID为pid的进程的PGID设置为pgid。如果pid和pgid相同，则由pid指定的进程将被设置为进程组首领；如果pid为0，则表示设置当前进程的PGID为pgid；如果pgid为0，则使用pid作为目标PGID。

setpgid函数成功时返回0，失败则返回-1并设置errno。

一个进程只能设置自己或者其子进程的PGID。并且，当子进程调用exec系列函数后，我们也不能再在父进程中对它设置PGID。

#### 　会话

一些有关联的进程组将形成一个会话（session）

```
#include＜unistd.h＞

pid_t setsid(void);
```

该函数不能由进程组的首领进程调用，否则将产生一个错误。

对于非组首领的进程，调用该函数不仅创建新会话，而且有如下额外效果
❑调用进程成为会话的首领，此时该进程是新会话的唯一成员。
❑新建一个进程组，其PGID就是调用进程的PID，调用进程成为该组的首领。
❑调用进程将甩开终端（如果有的话）。

该函数成功时返回新的进程组的PGID，失败则返回-1并设置errno。

Linux进程并未提供所谓会话ID（SID）的概念，但Linux系统认为它等于会话首领所在的进程组的PGID，并提供了如下函数来读取SID

```
pid_t getsid(pid_t pid);
```


#### 　用ps命令查看进程关系

```
$ps -o pid,ppid,pgid,sid,comm|less

PID PPID PGID SID COMMAND
1943 1942 1943 1943 bash
2298 1943 2298 1943 ps
2299 1943 2298 1943 less
```

ps和less命令的父进程是bash命令
这3条命令创建了1个会话（SID是1943）和2个进程组（PGID分别是1943和2298）。
bash命令的PID、PGID和SID都相同，很明显它既是会话的首领，也是组1943的首领。
ps命令则是组2298的首领，因为其PID也是2298。

![[Pasted image 20240625200240.png]]


### 　系统资源限制

Linux系统资源限制可以通过如下一对函数来读取和设置

```
#include＜sys/resource.h＞

int getrlimit(int resource,struct rlimit*rlim);

int setrlimit(int resource,const struct rlimit*rlim);

struct rlimit

{
rlim_t rlim_cur;
rlim_t rlim_max;
};

```


rlim_t是一个整数类型，它描述资源级别。
rlim_cur成员指定资源的软限制，rlim_max成员指定资源的硬限制。

软限制是一个建议性的、最好不要超越的限制，如果超越的话，系统可能向进程发送信号以终止其运行。


可以使用ulimit命令修改当前shell环境下的资源限制（软限制或/和硬限制），这种修改将对该shell启动的所有后续程序有效

![[Pasted image 20240625200847.png]]


### 　改变工作目录和根目录

获取进程当前工作目录和改变进程工作目录的函数

```
#include＜unistd.h＞

char* getcwd(char*buf,size_t size);

int chdir(const char*path);

int chroot(const char*path);
```

getcwd buf参数指向的内存用于存储进程当前工作目录的绝对路径名，其大小由size参数指定。

getcwd函数成功时返回一个指向目标存储区（buf指向的缓存区或是getcwd在内部动态创建的缓存区）的指针，失败则返回NULL并设置errno。

chroot  path参数指定要切换到的目标根目录。它成功时返回0，失败时返回-1并设置errno。

chroot并不改变进程的当前工作目录，所以调用chroot之后，我们仍然需要使用chdir(“/”)来将工作目录切换至新的根目录。



### 　服务器程序后台化

```
#include＜unistd.h＞

int daemon(int nochdir,int noclose);
```


nochdir参数用于指定是否改变工作目录，如果给它传递0，则工作目录将被设置为“/”（根目录），否则继续使用当前工作目录。noclose参数为0时，标准输入、标准输出和标准错误输出都被重定向到/dev/null文件，否则依然使用原来的设备。该函数成功时返回0，失败则返回-1并设置errno。