
### ipcs

当程序调用semget、shmget或者msgget时，就创建了这些共享资源的一个实例。Linux提供了ipcs命令，以观察当前系统上拥有哪些共享资源实例。

我们可以使用**ipcrm**命令来删除遗留在系统中的共享资源


### printk_level

echo 5 >/proc/sys/kernel/printk


