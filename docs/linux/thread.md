
根据运行环境和调度者的身份，线程可分为内核线程和用户线程。
内核线程，在有的系统上也称为LWP（Light Weight Process，轻量级进程），运行在内核空间，由内核来调度；用户线程运行在用户空间，由线程库来调度。


Linux上两个最有名的线程库是LinuxThreads和NPTL，它们都是采用1:1的方式实现的。
现代Linux上默认使用的线程库是NPTL, `getconf GNU_LIBPTHREAD_VERSION`


LinuxThreads线程库的内核线程是用clone系统调用创建的进程模拟的。clone系统调用和fork系统调用的作用类似：创建调用进程的子进程。

我们可以为clone系统调用指定CLONE_THREAD标志，这种情况下它创建的子进程与调用进程共享相同的虚拟地址空间、文件描述符和信号处理函数，这些都是线程的特点。

### 　创建线程和结束线程

```c
#include＜pthread.h＞

int pthread_create(pthread_t*thread,const pthread_attr_t*attr,void*(*start_routine)(void*),void*arg);

#include＜bits/pthreadtypes.h＞

typedef unsigned long int pthread_t;

```

thread参数是新线程的标识符，后续pthread_\*函数通过它来引用新线程。
attr参数用于设置新线程的属性。给它传递NULL表示使用默认线程属性。
start_routine和arg参数分别指定新线程将运行的函数及其参数。

pthread_create成功时返回0，失败时返回错误码。


```c
#include＜pthread.h＞

void pthread_exit(void*retval);
```

pthread_exit函数通过retval参数向线程的回收者传递其退出信息。

```c

int pthread_join(pthread_t thread,void**retval);
```
进程中的所有线程都可以调用pthread_join函数来回收其他线程
thread参数是目标线程的标识符，retval参数则是目标线程返回的退出信息。该函数会一直阻塞，直到被回收的线程结束为止。

该函数成功时返回0，失败则返回错误码。
![[Pasted image 20240628201724.png]]

有时候我们希望异常终止一个线程，即取消线程

```c
int pthread_cancel(pthread_t thread);
int pthread_setcancelstate(int state,int*oldstate);
int pthread_setcanceltype(int type,int*oldtype);
```

这两个函数的第一个参数分别用于设置线程的取消状态（是否允许取消）和取消类型（如何取消），第二个参数则分别记录线程原来的取消状态和取消类型。state参数有两个可选值：
❑PTHREAD_CANCEL_ENABLE，允许线程被取消。它是线程被创建时的默认取消状态。
❑PTHREAD_CANCEL_DISABLE，禁止线程被取消。这种情况下，如果一个线程收到取消请求，则它会将请求挂起，直到该线程允许被取消。

type参数也有两个可选值：
❑PTHREAD_CANCEL_ASYNCHRONOUS，线程随时都可以被取消。它将使得接收到取消请求的目标线程立即采取行动。
❑PTHREAD_CANCEL_DEFERRED，允许目标线程推迟行动，直到它调用了下面几个所谓的取消点函数中的一个：pthread_join、pthread_testcancel、pthread_cond_wait、pthread_cond_timedwait、sem_wait和sigwait。根据POSIX标准，其他可能阻塞的系统调用，比如read、wait，也可以成为取消点。

### 　线程属性

线程库定义了一系列函数来操作pthread_attr_t类型的变量，以方便我们获取和设置线程属性。

```c
#include＜pthread.h＞
/*初始化线程属性对象*/
int pthread_attr_init(pthread_attr_t*attr);

/*销毁线程属性对象。被销毁的线程属性对象只有再次初始化之后才能继续使用*/
int pthread_attr_destroy(pthread_attr_t*attr);

/*下面这些函数用于获取和设置线程属性对象的某个属性*/
int pthread_attr_getdetachstate(const pthread_attr_t*attr,int*detachstate);
int pthread_attr_setdetachstate(pthread_attr_t*attr,int detachstate);
int pthread_attr_getstackaddr(const pthread_attr_t*attr,void**stackaddr);
int pthread_attr_setstackaddr(pthread_attr_t*attr,void*stackaddr);
int pthread_attr_getstacksize(const pthread_attr_t*attr,size_t*stacksize);
int pthread_attr_setstacksize(pthread_attr_t*attr,size_t stacksize);
int pthread_attr_getstack(const pthread_attr_t*attr,void**stackaddr,size_t*stacksize);
int pthread_attr_setstack(pthread_attr_t*attr,void*stackaddr,size_t stacksize);
int pthread_attr_getguardsize(const pthread_attr_t*__attr,size_t*guardsize);
int pthread_attr_setguardsize(pthread_attr_t*attr,size_t guardsize);
int pthread_attr_getschedparam(const pthread_attr_t*attr,struct sched_param*param);
int pthread_attr_setschedparam(pthread_attr_t*attr,const struct sched_param*param);
int pthread_attr_getschedpolicy(const pthread_attr_t*attr,int*policy);
int pthread_attr_setschedpolicy(pthread_attr_t*attr,int policy);
int pthread_attr_getinheritsched(const pthread_attr_t*attr,int*inherit);
int pthread_attr_setinheritsched(pthread_attr_t*attr,int inherit);
int pthread_attr_getscope(const pthread_attr_t*attr,int*scope);
int pthread_attr_setscope(pthread_attr_t*attr,int scope);
```


### 　POSIX信号量

```c
#include＜semaphore.h＞

int sem_init(sem_t*sem,int pshared,unsigned int value);
int sem_destroy(sem_t*sem);
int sem_wait(sem_t*sem);
int sem_trywait(sem_t*sem);
int sem_post(sem_t*sem);
```
sem_init函数用于初始化一个未命名的信号量（POSIX信号量API支持命名信号量，不过本书不讨论它）。pshared参数指定信号量的类型。如果其值为0，就表示这个信号量是当前进程的局部信号量，否则该信号量就可以在多个进程之间共享。value参数指定信号量的初始值。

sem_destroy函数用于销毁信号量，以释放其占用的内核资源。

sem_trywait与sem_wait函数相似，不过它始终立即返回，而不论被操作的信号量是否具有非0值，相当于sem_wait的非阻塞版本。当信号量的值非0时，sem_trywait对信号量执行减1操作。当信号量的值为0时，它将返回-1并设置errno为EAGAIN。

sem_post函数以原子操作的方式将信号量的值加1。当信号量的值大于0时，其他正在调用sem_wait等待信号量的线程将被唤醒。

函数成功时返回0，失败则返回-1并设置errno。

### 　互斥锁

互斥锁（也称互斥量）可以用于保护关键代码段，以确保其独占式的访问

```c
#include＜pthread.h＞

int pthread_mutex_init(pthread_mutex_t*mutex,const pthread_mutexattr_t*mutexattr);
int pthread_mutex_destroy(pthread_mutex_t*mutex);
int pthread_mutex_lock(pthread_mutex_t*mutex);
int pthread_mutex_trylock(pthread_mutex_t*mutex);
int pthread_mutex_unlock(pthread_mutex_t*mutex);

pthread_mutex_t mutex=PTHREAD_MUTEX_INITIALIZER;
```

第一个参数mutex指向要操作的目标互斥锁，互斥锁的类型是pthread_mutex_t结构体。

pthread_mutex_init函数用于初始化互斥锁。
mutexattr参数指定互斥锁的属性。如果将它设置为NULL，则表示使用默认属性。

宏PTHREAD_MUTEX_INITIALIZER实际上只是把互斥锁的各个字段都初始化为0。

pthread_mutex_destroy函数用于销毁互斥锁，以释放其占用的内核资源。销毁一个已经加锁的互斥锁将导致不可预期的后果。

pthread_mutex_lock函数以原子操作的方式给一个互斥锁加锁。如果目标互斥锁已经被锁上，则pthread_mutex_lock调用将阻塞，直到该互斥锁的占有者将其解锁。

pthread_mutex_trylock与pthread_mutex_lock函数类似，不过它始终立即返回，而不论被操作的互斥锁是否已经被加锁，相当于pthread_mutex_lock的非阻塞版本。当目标互斥锁未被加锁时，pthread_mutex_trylock对互斥锁执行加锁操作。当互斥锁已经被加锁时，pthread_mutex_trylock将返回错误码EBUSY。

pthread_mutexattr_t结构体定义了一套完整的互斥锁属性。线程库提供了一系列函数来操作pthread_mutexattr_t类型的变量，以方便我们获取和设置互斥锁属性。
```c
#include＜pthread.h＞

/*初始化互斥锁属性对象*/
int pthread_mutexattr_init(pthread_mutexattr_t*attr);

/*销毁互斥锁属性对象*/
int pthread_mutexattr_destroy(pthread_mutexattr_t*attr);

/*获取和设置互斥锁的pshared属性*/
int pthread_mutexattr_getpshared(const pthread_mutexattr_t*attr,int*pshared);
int pthread_mutexattr_setpshared(pthread_mutexattr_t*attr,int pshared);

/*获取和设置互斥锁的type属性*/
int pthread_mutexattr_gettype(const pthread_mutexattr_t*attr,int*type);
int pthread_mutexattr_settype(pthread_mutexattr_t*attr,int type);
```

❑PTHREAD_PROCESS_SHARED。互斥锁可以被跨进程共享。

❑PTHREAD_PROCESS_PRIVATE。互斥锁只能被和锁的初始化线程隶属于同一个进程的线程共享。

互斥锁属性type指定互斥锁的类型。Linux支持如下4种类型的互斥锁：

❑PTHREAD_MUTEX_NORMAL，普通锁。这是互斥锁默认的类型。当一个线程对一个普通锁加锁以后，其余请求该锁的线程将形成一个等待队列，并在该锁解锁后按优先级获得它。

❑PTHREAD_MUTEX_ERRORCHECK，检错锁。一个线程如果对一个已经加锁的检错锁再次加锁，则加锁操作返回EDEADLK。

❑PTHREAD_MUTEX_RECURSIVE，嵌套锁。这种锁允许一个线程在释放锁之前多次对它加锁而不发生死锁。

❑PTHREAD_MUTEX_DEFAULT，默认锁。一个线程如果对一个已经加锁的默认锁再次加锁


**死锁**使得一个或多个线程被挂起而无法继续执行，而且这种情况还不容易被发现。
在一个线程中对一个已经加锁的普通锁再次加锁，将导致死锁。
如果两个线程按照不同的顺序来申请两个互斥锁，也容易产生死锁

### 　条件变量

如果说互斥锁是用于同步线程对共享数据的访问的话，那么条件变量则是用于在线程之间同步共享数据的值。条件变量提供了一种线程间的通知机制：当某个共享数据达到某个值的时候，唤醒等待这个共享数据的线程。

```c
#include＜pthread.h＞

int pthread_cond_init(pthread_cond_t*cond,const pthread_condattr_t*cond_attr);
int pthread_cond_destroy(pthread_cond_t*cond);
int pthread_cond_broadcast(pthread_cond_t*cond);
int pthread_cond_signal(pthread_cond_t*cond);
int pthread_cond_wait(pthread_cond_t*cond,pthread_mutex_t*mutex);

pthread_cond_t cond=PTHREAD_COND_INITIALIZER;

```

这些函数的第一个参数cond指向要操作的目标条件变量，条件变量的类型是pthread_cond_t结构体。

pthread_cond_init函数用于初始化条件变量。cond_attr参数指定条件变量的属性。如果将它设置为NULL，则表示使用默认属性。条件变量的属性不多，而且和互斥锁的属性类型相似

[[test_thread.cpp]]
