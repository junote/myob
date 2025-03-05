
 ####  search lib 

 `ldconfig -p | grep libjpeg`
#### search deb 

`dpkg -l |grep busybox`
 
 ####  linux dev driver

``` shell
$ ls -l /dev/vda
brw-rw---- 1 root disk 252, 0 Jun 21 17:06 /dev/vda

$ readlink /sys/dev/block/252\:0/device/driver
../../../../bus/virtio/drivers/virtio_blk

$ ls -l /sys/dev/*/*/device/driver
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/block/11:0/device/driver -> ../../../../../../../bus/scsi/drivers/sr
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/block/2:0/device/driver -> ../../../bus/platform/drivers/floppy
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/block/252:0/device/driver -> ../../../../bus/virtio/drivers/virtio_blk
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/21:0/device/driver -> ../../../../../../../bus/scsi/drivers/sr
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/226:0/device/driver -> ../../../bus/pci/drivers/virtio-pci
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/226:128/device/driver -> ../../../bus/pci/drivers/virtio-pci
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/240:0/device/driver -> ../../../../../../../bus/hid/drivers/hid-generic
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/243:0/device/driver -> ../../../../../../../bus/scsi/drivers/sr
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/248:0/device/driver -> ../../../bus/pnp/drivers/rtc_cmos
lrwxrwxrwx 1 root root 0 Jun 21 17:06 /sys/dev/char/29:0/device/driver -> ../../../bus/pci/drivers/virtio-pci
...
```


#### usb speed

usb2.0 speed read about 25M, write 35M
```
read
sh -c "sync && echo 3 > /proc/sys/vm/drop_caches"
dd if=./data of=/dev/null bs=1M count=200
write
sh -c "sync && echo 3 > /proc/sys/vm/drop_caches"
dd if=/dev/zero of=./data bs=1M count=200

dd if=/dev/sda of=/dev/null bs=1M count=100

write
dd if=/dev/zero of=./data bs=1M count=200

dd if=/dev/zero of=/dev/sda bs=1M count=100

ext4load usb 0 10000000 onl-loader-fit.0.itb
12910620 bytes read in 348 ms (35.4 MiB/s)
```


#### ip

- show vlan number: ip -d link show

- ip -d link show

- tcpdump vlan id, tcpdump to capture VLAN 1000 or VLAN 501
    
      
    tcpdump -vv -i eth1 '( vlan and ( ether[14:2] & 0xfff == 1000 or ether[14:2] & 0xfff == 501 ) ) and ( ip host 10.1.1.98 or ip host 10.1.1.99 )'  
    
- `tcpdump -i eth1-01 vlan 200 and tcp`
```
$ sudo tcpdump host x.x.x.x
To filter by interface:

$ sudo tcpdump -i eth0
To filter by source:

$ sudo tcpdump src x.x.x.x
To filter by destination:

$ sudo tcpdump dst x.x.x.x
To filter by protocol:

$ sudo tcpdump icmp
```


#### gdb

- args： gdb --args prog argslist
- break

- **break** \[_Function Name_] class:fun
- **break** \[_File Name_]:\[_Line Number_]
- **break** \[_Line Number_]**
- break*\[_Address_]
- **break** \[_..._] if \[_Condition_]
- **break** \[_..._] thread \[_Thread-id_]


#### ccache

ccache 是一个编译器缓存工具，主要用于加速 C/C++ 程序的编译过程。它通过缓存编译过的结果，避免重复编译相同的代码，从而减少编译时间。具体来说，当你编译一个项目时，ccache 会检查是否已经编译过相同的源文件，如果有，它会直接使用缓存中的结果，而不是重新编译。

clean ccach `ccache -C`



#### gpio

```
apt install libgpiod
apt install libgpiod2
gpiodetect
gpioinfo
gpioset gpiochip3 9=1
gpioset 3 9=1
gpioset 3 9=0
gpioget gpiochip3 8
gpioget 3 8

echo 44 > /sys/class/gpio/export
echo 44 > /sys/class/gpio/unexport
echo in > /sys/class/gpio/gpio44/direction
cat /sys/class/gpio/gpio44/value
echo 0 > /sys/class/gpio/gpio44/value


```




#### iperf
>  wget https://iperf.fr/download/source/iperf-3.1.3-source.tar.gz
tar zxvf iperf-3.1.3-source.tar.gz
cd iperf-3.1.3
./configure
make
make install

>服务端：收包，使用 -s 参数指定， iperf3 -s
客户端：发包，使用 -c xx.xx.xx.xx 来指定要往哪个服务端发包， iperf3 -c 172.20.20.200
iperf3 还有更多的参数，其中有一些是客户端专用的，有一些是服务端专用的，也有一些是二者共用的。

>具体可以前往这个地址，进行查阅：https://www.cnblogs.com/yingsong/p/5682080.html

```

常用的参数有

-u：发送 UDP 包，仅客户端可用，服务端默认 tcp udp 都可以接收
-b：指定发送速率（比如 100M），发送端不受限速影响，如果有限速，也只是接收端有影响
-p：后接服务端监听的端口
-i：设置带宽报告的时间间隔，单位为秒
-t：设置测试的时长，单位为秒
-w：设置tcp窗口大小，一般可以不用设置，默认即可
-B：绑定客户端的ip地址
-4：指定 ipv4
-n：指定传输的字节数
-f：格式化带宽数输出，后接单位，比如 K，M
--get-server-output：在客户端直接获取服务端输出的结果

iperf3 的输出结果可以分为两类

一类是，详细的带宽数据
一类是，最终的带宽数据
如果你像我一样加上 --get-server-output ，可以看到服务端输出的报告。

接下来看一下，输出的报告有哪些内容。

第一列 Interval：测试的时长
第二列 Transfer：在 Interval 时长里，传输的数据量
第三列 Bitrate：传输速率
第四列 Jitter：网络抖动，连续发送数据包时延差值的平均值，越小说明网络质量越好
第五列 Lost/Total Datagrams：丢失的数据包与发送的总数据包

```



#### screen

screen /dev/ttyS0 9600
ctrl+A i info
ctrl+A k exit


#### coredump

```
To enable core dumps, first check the maximum core dump size:

$ ulimit -c
If the result of this is zero (i.e. no core dump will be produced), set the limit to the maximum:

$ ulimit -c unlimited
A core dump will now be generated and placed in the location specified by /proc/sys/kernel/core_pattern. Check this location by running:

$ cat /proc/sys/kernel/core_pattern
On CSE systems (and many other systems), the default settings result in the output:

core
This means that any core dumps will be placed in the current directory in a file named core.

You can change this location using:

$ echo "<desired-file-path>/<desired-file-name>" > /proc/sys/kernel/core_pattern


Start a GDB session with the program binary and coredump file

$ gdb <binary-file> <core-dump-file>


ulimit -c unlimited
echo "/root/coredump" > /proc/sys/kernel/core_pattern
 
 


```


#### ifconfig_mac

ifconfig eth1 hw ether 00:01:9f:ef:66:77


#### date

```
date -s "20120523 01:01:01" 
ntpdate -u cn.pool.ntp.org 
timedatectl set-timezone Asia/Shanghai

```


#### dd
```

sudo dd if=/dev/mmcblk0 of=~/sd-card-copy.img bs=1M status=progress

```


#### socket_port


```

sudo apt install iproute2
sudo ss -tulpn
-t, --tcp: To see all TCP sockets
-u, --udp: To see all UDP sockets
-l, --listening: To see all listening sockets
-p, processes: To see which processes are using sockets
-n, --numeric: Use this option if you want to see a port number instead of service names


sudo apt-get install net-tools
sudo netstat –tulnp
-t, --tcp: To see all TCP sockets
-u, --udp: To see all UDP sockets
-l, --listening: To see all listening sockets
-p, processes: To see which processes are using sockets
-n, --numeric: Use this option if you want to see a port number instead of service names

apt-get install lsof
sudo lsof -nP -iTCP -sTCP:LISTEN

Check open ports using the Nmap utility
sudo apt install nmap
sudo nmap –sT –p-65535 ip-address


```