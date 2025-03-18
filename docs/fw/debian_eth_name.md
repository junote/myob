
debian12启动的时候会更改eth0的名称

```
root@localhost:~# dmesg |grep -i end
[   17.746026] signverif: Parsing issue end = 0, start = 2032830208
[   39.132333] macb ff0b0000.ethernet end0: renamed from eth0
```

#### 修改uboot的启动参数

添加

```
net.ifnames=0
```

#### systemd 修改

**create** a file `/etc/systemd/network/10-eth1p1.link`

```
ex. name: ens86f0
[Match]
MACAddress=aa:bb:cc:dd:ee:ff
[Link]
Name=eth1p1
```

`systemctl restart systemd-udev-trigger.service`



[networking - Debian 11/12 rename network interfaces - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/733080/debian-11-12-rename-network-interfaces)

[debian - Rename ether interface with systemd-networkd without reboot - Unix & Linux Stack Exchange](https://unix.stackexchange.com/questions/703012/rename-ether-interface-with-systemd-networkd-without-reboot)

