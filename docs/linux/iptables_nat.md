

```
echo 1 > /proc/sys/net/ipv4/ip_forward

ip link add link eth1 name eth1.99 type vlan id 99
ip link set dev eth1.99 up
ip addr add 192.168.99.1/24 dev eth1.99




iptables-legacy -t nat -A PREROUTING -p tcp -i  eth0 --dport 9022 -j DNAT --to-destination 192.168.99.22:22
iptables-legacy -t nat -A POSTROUTING -p tcp -o eth0 --sport 22 -j SNAT --to-source 10.13.14.11:9022
iptables-legacy -t nat -A PREROUTING -p tcp -i  eth0 --dport 12355 -j DNAT --to-destination 192.168.99.22
iptables-legacy -t nat -A POSTROUTING -p tcp -o eth0 --sport  12355 -j SNAT --to-source 10.13.14.11
iptables-legacy -t nat -A PREROUTING -p tcp -i  eth0 --dport 12300 -j DNAT --to-destination 192.168.99.22
iptables-legacy -t nat -A POSTROUTING -p tcp -o eth0 --sport  12300 -j SNAT --to-source 10.13.14.11
iptables-legacy -t nat -A PREROUTING -p tcp -i  eth0 --dport 50004 -j DNAT --to-destination 192.168.99.22
iptables-legacy -t nat -A POSTROUTING -p tcp -o eth0 --sport  50004 -j SNAT --to-source 10.13.14.11


#xr
ip route del default
ip route add default via 192.168.99.1 via br0


```

