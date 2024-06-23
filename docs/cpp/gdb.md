
### 用gdb调试多进程程序

如果一个进程通过fork系统调用创建了子进程，gdb会继续调试原来的进程，子进程则正常运行。那么该如何调试子进程呢

#### 单独调试子进程

可以先运行服务器，然后找到目标子进程的PID，再将其附加（attach）到gdb调试器上，

```
$./cgisrv 127.0.0.1 12345
$ps-ef|grep cgisrv
shuang 4183 4182 0 12:25 pts/4 00:00:00./cgisrv 127.0.0.1 12345

$gdb
(gdb)attach 4183/*将子进程4183附加到gdb调试器*/


```

gdb调试器的选项follow-fork-mode允许我们选择程序在执行fork系统调用后是继续调试父进程还是调试子进程。

```
(gdb)set follow-fork-mode mode

(gdb)b processpool.h:264
Breakpoint 1 at 0x8049787:file processpool.h,line 264.
(gdb)r 127.0.0.1 12345
```



### 用gdb调试多线程程序

❑info threads，显示当前可调试的所有线程。gdb会为每个线程分配一个ID，我们可以使用这个ID来操作对应的线程。ID前面有“*”号的线程是当前被调试的线程。

❑thread ID，调试目标ID指定的线程。

❑set scheduler-locking[off|on|step]。调试多线程程序时，默认除了被调试的线程在执行外，其他线程也在继续执行，但有的时候我们希望只让被调试的线程运行。这可以通过这个命令来实现。该命令设置scheduler-locking的值：off表示不锁定任何线程，即所有线程都可以继续执行，这是默认值；on表示只有当前被调试的线程会继续执行；step表示在单步执行的时候，只有当前线程会执行。

