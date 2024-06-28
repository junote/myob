
In IP-based computer networks, virtual routing and forwarding (VRF) is a technology that allows multiple instances of a routing table to co-exist within the same router at the same time.
One or more logical or physical interfaces may have a VRF and these VRFs do not share routes therefore the packets are only forwarded between interfaces on the same VRF.
VRFs are the TCP/IP layer 3 equivalent of a VLAN. Because the routing instances are independent, 
the same or overlapping IP addresses can be used without conflicting with each other.
Network functionality is improved because network paths can be segmented without requiring multiple routers.[1]

在基于IP的计算机网络中，虚拟路由和转发（VRF）是一种技术，它允许一个路由表的多个实例同时存在于同一台路由器中。一个或多个逻辑或物理接口可以有一个VRF，
这些VRF不共享路由，因此数据包只在同一VRF的接口之间转发。VRF是TCP/IP第三层的VLAN的等同物。因为路由实例是独立的，所以可以使用相同或重叠的IP地址，
而不会相互冲突。网络功能得到了改善，因为网络路径可以被分割而不需要多个路由器[1]。

VRF：virtual routing and forwording 虚拟路由转发，又称为VPN instance
在物理设备上创建多个VPN实例，每个VPN实例拥有独立的接口，路由表和路由协议进程等，相当于又虚拟出一台同等关系的路由器，基本上完全独立
可以看做是一台新的设备来使用。这种虚拟类型属于一虚多。
应用场景
公司需要部署两台防火墙，一台外网防火墙和一台内网防火墙，为了节省成本，我们可以购买一台物理防火墙，然后将其虚拟成两台。
运营商为了不同的客户网段重叠的问题，在做MPLS的时候，单独使用一个VRF系统针对一个客户。
作用：通过一台三层转发设备上创建多张路由表实现业务的隔离，常用于MPLS VPN、防火墙。

```

sudo modprobe vrf
ip link add red type vrf table 111
ip link add green type vrf table 2

ip link set dev red up
ip link set dev green up

ip -br link show type vrf
ip -d link show type vrf

ip link set ens37 master red
ip link set ens37 master green

ip link show vrf red
ip addr show vrf red
ip route show vrf red
 ip route get 10.2.1.40 vrf red

ip route table show -a
ip route show 


ip addr show 
ip addr show vrf MGMT

ip link show type vrf
```