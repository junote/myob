
```
槽位 _＃/ 子槽位 _＃/ 端口_＃

进入特权模式
    Router>enable

进入配置模式
    Router#configure terminal

特权模式下查看全局配置
    Router#show running-config

非特权模式下查看全局配置（+do 在非特权模式下使用do show查看配置）
    Router(config)#do show running-config

重启
    Router#reload

保存配置
    Router#write

清空配置
    Router#write erase

删除命令
    Router(config-if)#no ip address

修改主机名
    Router(config)#hostname R1
    
设置进入特权模式的密码
    Router(config)#enable password cisco@123

设置远程登录
    line vty 0 4
       password cisco@123
       login                //允许登录
       transport input telnet    //允许通过的协议

远程登录时查看日志信息
    Router#terminal monitor

路由功能
    Switch(config)#ip routing     //开启路由功能
    Switch(config)#no ip routing     //关闭路由功能
    Switch(config)#ip default-gateway 192.168.1.254 //设置默认网关

查看MAC地址表
    Switch#show mac address-table

设置全/半双工
    Router(config-if)#duplex full/half

路由管理距离（类似Huawei 优先级）
    Router(config)#ip route 0.0.0.0 0.0.0.0 1.1.1.1 ?
          <1-255>    Distance metric for this route

    Protocol    Distance
    Direct        0
    Static        1
    Rip        120
    OSPF        110
    Eigrp        90（170）


CDP协议（思科私有，与LLDP相同）
    Router#show cdp  neighbors

VLAN，接口
    
    Switch(config)#vlan 10,20,30    //同时创建多个vlan

    interface Ethernet0/1
     switchport access vlan 10
      switchport mode access

    interface Ethernet0/2
      switchport trunk encapsulation dot1q    //配置封装模式（Cisco默认私有模式ISL）
      switchport trunk native vlan 10    //本征vlan（类似华为pvid）
     switchport mode trunk    
     switchport trunk allowed vlan all    
     switchport trunk allowed vlan 10,20
     switchport trunk allowed vlan remove 10//不允许某个vlan通过

    子接口
    interface Ethernet0/1.10        //创建子接口
     encapsulation dot1Q 10            //封装vlan10
     ip address 192.168.10.1 255.255.255.0

DHCP
    ip dhcp excluded-address 192.168.10.250 192.168.10.254    
    ip dhcp pool dhcp-pool                    
     network 192.168.10.0 255.255.255.0            
     default-router 192.168.10.254
     dns-server 114.114.114.114

    Switch(config-if)#ip helper-address 192.168.20.254 //dhcp中继

    Router#show ip dhcp conflict         //查看地址冲突
    Router#clear ip dhcp conflict *        //清除dhcp冲突地址
    Router#show dhcp server

ACL
    Router(config)#access-list 1 permit 192.168.10.1 0.0.0.0     //基本acl
    Router(config)#access-list 100 permit tcp 192.168.10.1 0.0.0.0 eq 80 1.1.1.1 0.0.0.0 eq 8000     //扩展acl
    
    //基于名称的acl（可以设置规则序号）
    Router(config)#ip access-list standard ciaco-acl
    Router(config-std-nacl)#11 permit 192.168.1.1 0.0.0.0

    Router(config-if)#ip access-group 1 in/out    //在接口上应用acl


NAT
    interface Ethernet0/0
     ip address 192.168.10.1 255.255.255.0
     ip nat inside                //内网人口            

    interface Ethernet0/1
     ip address 200.1.1.1 255.255.255.0
     ip nat outside                //外网出口


    access-list 10 permit 192.168.10.0 0.0.0.255        
    ip nat inside source list 10 interface Ethernet0/1        //nat转换
    ip nat inside source static tcp 192.168.10.23 23 200.1.1.1 23    //端口映射


链路聚合
    Switch(config)#interface range ethernet 0/2 - 3        //创建接口组（类似port-group）
    Switch(config-if-range)#channel-group 12 mode active    //创建channel id（两端id不需要相同），设置模式
    // shutdown状态下配置，如果不成功更换channel id
    Switch#show etherchannel summary             //查看命令


STP
    Switch(config)#spanning-tree mode ?        
      mst         Multiple spanning tree mode    //多生成树（802.1s）
      pvst        Per-Vlan spanning tree mode    //传统模式(802.1D)
      rapid-pvst  Per-Vlan rapid spanning tree mode    //快速生成树(802.1w)

    Switch(config)#spanning-tree vlan 10 priority 0 //设置优先级


VTP（思科私有用于vlan同步，只用于交换机间的接口是trunk口）
    {vtp是危险协议，推荐修改为透明模式}
    Switch(config)#vtp mode ?
      client       Set the device to client mode.        //客户端模式（用于同步配置，不能创建修改删除vlan）
      off          Set the device to off mode.        //关闭
      server       Set the device to server mode.        //服务器模式（同步配置，可以创建修改删除vlan）
      transparent  Set the device to transparent mode.    //透明模式（推荐配置，不会被同步）

    vtp domain cisco     //创建vtp域名
    vtp version 2        //修改版本（v1,v2,v3存在版本兼容问题）
    vtp password cisco@123    //设置认证密码


HSRP（思科私有，类似vrrp）

    interface Ethernet0/1
     ip address 192.168.1.2 255.255.255.0
     standby 12 ip 192.168.1.254        //设置虚拟IP（standby id 两端必须一致）
     standby 12 priority 110        //设置优先级（默认100，越大越优）
     standby 12 preempt            //开启抢占


PPP
    username admin password 0 cisco@123        //设置用户，密码
    interface Serial1/0
     ip address 192.168.10.1 255.255.255.0    
     encapsulation ppp            //封装为PPP
     ppp authentication chap/pap        //认证模式为chap或pap

    interface Serial1/0
     ip address 192.168.10.2 255.255.255.0
     encapsulation ppp
     ppp chap hostname admin
     ppp chap password ciaco@123
     ppp pap sent-username admin password ciaco@123

PPPoE
    Server：
    bba-group pppoe Cisco
     virtual-template 1

    interface Ethernet0/1
     no ip address
     pppoe enable group Cisco

    username admin password 0 cisco@123
    ip local pool pppoe-pool 200.1.1.2 200.1.1.10    //创建可分配的地址池
    interface Virtual-Template1
     ip address 200.1.1.1 255.255.255.0
     peer default ip address pool pppoe-pool
     ppp authentication chap


    Client：
    interface Ethernet0/1
     no ip address
     pppoe enable
     pppoe-client dial-pool-number 1

    interface Dialer1
     ip address negotiated
      ip mtu 1492            //1500-ppp头部
     encapsulation ppp
     ip tcp adjust-mss 1452        //1500-(ppp头部+ip头部+tcp头部)
     dialer pool 1
      ppp authentication chap callin    //启用chap验证
     ppp chap hostname admin
     ppp chap password 0 cisco@123

GRE
    interface Tunnel10
     ip address 10.1.1.1 255.255.255.0
     tunnel source 1.1.1.1            //本端公网IP
     tunnel destination 2.2.2.2        //对端公网IP

```