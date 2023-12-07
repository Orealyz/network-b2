# TP6 : STP, OSPF, bigger infra
## Sommaire

- [TP6 : STP, OSPF, bigger infra](#tp6--stp-ospf-bigger-infra)
  - [Sommaire](#sommaire)
  - [0. Setup](#0-setup)
  - [I. STP](#i-stp)
  - [II. OSPF](#ii-ospf)
  - [III. DHCP relay](#iii-dhcp-relay)
  - [IV. Bonus](#iv-bonus)
    - [1. ACL](#1-acl)

## 0. Setup


## I. STP


![Topo STP](./img/topo_stp.png)


```

IOU4#show spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        100
             Port        3 (Ethernet0/2)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  300 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Altn BLK 100       128.2    P2p
Et0/2               Root FWD 100       128.3    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
Et1/3               Desg FWD 100       128.8    P2p
[...]

```

🌞 **Altérer le spanning-tree** en désactivant un port


```
IOU4(config)#interface ethernet 0/2
IOU4(config-if)#shutdown
IOU4(config-if)#exit
IOU4(config)#exit
IOU4#
*Dec  1 14:24:17.404: %LINK-5-CHANGED: Interface Ethernet0/2, changed state to administratively down
*Dec  1 14:24:18.404: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/2, changed state to down
IOU4#enable
*Dec  1 14:24:18.741: %SYS-5-CONFIG_I: Configured from console by console
IOU4#show spanning-tree

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     aabb.cc00.0100
             Cost        200
             Port        2 (Ethernet0/1)
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     aabb.cc00.0300
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Et0/0               Desg FWD 100       128.1    P2p
Et0/1               Root LIS 100       128.2    P2p
Et0/3               Desg FWD 100       128.4    P2p
Et1/0               Desg FWD 100       128.5    P2p
Et1/1               Desg FWD 100       128.6    P2p
Et1/2               Desg FWD 100       128.7    P2p
Et1/3               Desg FWD 100       128.8    P2p
[...]
```



🌞 **Altérer le spanning-tree** en modifiant le coût d'un lien


```
IOU4(config)#interface ethernet 0/2
IOU4(config-if)#spanning-tree cost 50
IOU4(config-if)#exit
IOU4(config)#exit

```

🦈 **`tp6_stp.pcapng`**



[stp](gsn3.pcapng)

![Capture](gsn3.pcapng)
## II. OSPF

![Topo OSPF](./img/topo_ospf.png)




🌞 **Montez la topologie**

```
waf.tp6.b1> show ip

NAME        : waf.tp6.b1[1]
IP/MASK     : 10.6.1.11/24
GATEWAY     : 10.6.1.254
DNS         :
MAC         : 00:50:79:66:68:01
LPORT       : 20055
RHOST:PORT  : 127.0.0.1:20056
MTU         : 1500

```

```
R5#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.6.52.1       YES NVRAM  up                    up  
FastEthernet0/1            10.0.3.16       YES DHCP   up                    up  
FastEthernet1/0            unassigned      YES NVRAM  administratively down down
FastEthernet2/0            unassigned      YES NVRAM  administratively down down
NVI0                       10.6.52.1       YES unset  up                    up  
R5#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/24/36 ms

```

Je ping meo.tp6.b1 pour montrer que les pings fonctionnes correctement.
```
waf.tp6.b1> ping 10.6.2.11

84 bytes from 10.6.2.11 icmp_seq=1 ttl=63 time=21.243 ms
84 bytes from 10.6.2.11 icmp_seq=2 ttl=63 time=18.903 ms
^C

```

On va pas ajouter toutes les routes sur tous les routeurs, ce serait une giga plaie à faire, à maintenir, et peu résilient.

OSPF donc.

🌞 **Configurer OSPF sur tous les routeurs**


```
R1#show running-config

interface FastEthernet0/0
 ip address 10.6.41.1 255.255.255.252
 duplex auto
 speed auto

interface FastEthernet0/1
 ip address 10.6.21.1 255.255.255.252
 duplex auto
 speed auto

interface FastEthernet1/0
 ip address 10.6.13.1 255.255.255.252
 duplex auto
 speed auto

interface FastEthernet2/0
 ip address 10.6.3.254 255.255.255.0
 duplex auto
 speed auto

router ospf 1
 router-id 1.1.1.1
 log-adjacency-changes
 network 10.6.1.0 0.0.0.255 area 0
 network 10.6.3.0 0.0.0.255 area 0
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.13.0 0.0.0.255 area 0
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.21.0 0.0.0.255 area 0
 network 10.6.41.0 0.0.0.3 area 0

```

```
R2#sh running-config

interface FastEthernet0/0
 ip address 10.6.21.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.23.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.52.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 2.2.2.2
 log-adjacency-changes
 network 10.6.21.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0
 network 10.6.52.0 0.0.0.3 area 1

```

```
R3#sh running-config

interface FastEthernet0/0
 ip address 10.6.13.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.23.1 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 3.3.3.3
 log-adjacency-changes
 network 10.6.13.0 0.0.0.3 area 0
 network 10.6.23.0 0.0.0.3 area 0

```



```
R4#sh running-conf

interface FastEthernet0/0
 ip address 10.6.1.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address 10.6.2.254 255.255.255.0
 duplex auto
 speed auto
!
interface FastEthernet1/0
 ip address 10.6.41.2 255.255.255.252
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 4.4.4.4
 log-adjacency-changes
 network 10.6.1.0 0.0.0.255 area 3
 network 10.6.2.0 0.0.0.255 area 3
 network 10.6.41.0 0.0.0.3 area 0
```

```
R5#sh running-conf

interface FastEthernet0/0
 ip address 10.6.52.1 255.255.255.252
 ip nat inside
 ip virtual-reassembly
 duplex auto
 speed auto
!
interface FastEthernet0/1
 ip address dhcp
 duplex auto
 speed auto
!
interface FastEthernet1/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
interface FastEthernet2/0
 no ip address
 shutdown
 duplex auto
 speed auto
!
router ospf 1
 router-id 5.5.5.5
 log-adjacency-changes
 network 10.6.52.0 0.0.0.3 area 1
 default-information originate always

```

```
R1#show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:36    10.6.21.2       FastEthernet0/1
4.4.4.4           1   FULL/DR         00:00:32    10.6.41.2       FastEthernet0/0
3.3.3.3           1   FULL/BDR        00:00:36    10.6.13.2       FastEthernet1/0
R1#show ip route
Gateway of last resort is 10.6.21.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet1/0
O IA    10.6.1.0/24 [110/20] via 10.6.41.2, 00:57:51, FastEthernet0/0
O IA    10.6.2.0/24 [110/20] via 10.6.41.2, 00:57:51, FastEthernet0/0
C       10.6.3.0/24 is directly connected, FastEthernet2/0
C       10.6.21.0/30 is directly connected, FastEthernet0/1
O       10.6.23.0/30 [110/11] via 10.6.13.2, 00:26:13, FastEthernet1/0
C       10.6.41.0/30 is directly connected, FastEthernet0/0
O IA    10.6.52.0/30 [110/11] via 10.6.21.2, 00:30:47, FastEthernet0/1
O*E2 0.0.0.0/0 [110/1] via 10.6.21.2, 00:12:09, FastEthernet0/1

```

```
R2#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:31    10.6.21.1       FastEthernet0/0
3.3.3.3           1   FULL/BDR        00:00:30    10.6.23.1       FastEthernet0/1
5.5.5.5           1   FULL/BDR        00:00:33    10.6.52.1       FastEthernet1/0
R2#sh ip route

Gateway of last resort is 10.6.52.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/11] via 10.6.21.1, 00:32:02, FastEthernet0/0
O IA    10.6.1.0/24 [110/30] via 10.6.21.1, 00:32:02, FastEthernet0/0
O IA    10.6.2.0/24 [110/30] via 10.6.21.1, 00:32:02, FastEthernet0/0
O       10.6.3.0/24 [110/11] via 10.6.21.1, 00:32:02, FastEthernet0/0
C       10.6.21.0/30 is directly connected, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet0/1
O       10.6.41.0/30 [110/20] via 10.6.21.1, 00:32:03, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet1/0
O*E2 0.0.0.0/0 [110/1] via 10.6.52.1, 00:13:25, FastEthernet1/0

```

```
R3#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/DR         00:00:37    10.6.13.1       FastEthernet0/0
2.2.2.2           1   FULL/DR         00:00:37    10.6.23.2       FastEthernet0/1
R3#sh ip route

Gateway of last resort is 10.6.23.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
C       10.6.13.0/30 is directly connected, FastEthernet0/0
O IA    10.6.1.0/24 [110/30] via 10.6.13.1, 00:27:54, FastEthernet0/0
O IA    10.6.2.0/24 [110/30] via 10.6.13.1, 00:27:54, FastEthernet0/0
O       10.6.3.0/24 [110/11] via 10.6.13.1, 00:27:54, FastEthernet0/0
O       10.6.21.0/30 [110/20] via 10.6.23.2, 00:27:54, FastEthernet0/1
                     [110/20] via 10.6.13.1, 00:27:54, FastEthernet0/0
C       10.6.23.0/30 is directly connected, FastEthernet0/1
O       10.6.41.0/30 [110/20] via 10.6.13.1, 00:27:55, FastEthernet0/0
O IA    10.6.52.0/30 [110/11] via 10.6.23.2, 00:27:55, FastEthernet0/1
O*E2 0.0.0.0/0 [110/1] via 10.6.23.2, 00:13:51, FastEthernet0/1

```

```
R4#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/BDR        00:00:30    10.6.41.1       FastEthernet1/0
R4#sh ip route

Gateway of last resort is 10.6.41.1 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 8 subnets, 2 masks
O       10.6.13.0/30 [110/2] via 10.6.41.1, 01:58:14, FastEthernet1/0
C       10.6.1.0/24 is directly connected, FastEthernet0/0
C       10.6.2.0/24 is directly connected, FastEthernet0/1
O       10.6.3.0/24 [110/2] via 10.6.41.1, 01:58:14, FastEthernet1/0
O       10.6.21.0/30 [110/11] via 10.6.41.1, 00:58:42, FastEthernet1/0
O       10.6.23.0/30 [110/12] via 10.6.41.1, 00:28:29, FastEthernet1/0
C       10.6.41.0/30 is directly connected, FastEthernet1/0
O IA    10.6.52.0/30 [110/12] via 10.6.41.1, 00:33:02, FastEthernet1/0
O*E2 0.0.0.0/0 [110/1] via 10.6.41.1, 00:14:25, FastEthernet1/0

```

```
R5#sh ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address         Interface
2.2.2.2           1   FULL/DR         00:00:38    10.6.52.2       FastEthernet0/0

R5#sh ip route


Gateway of last resort is 10.0.3.2 to network 0.0.0.0

     10.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
O IA    10.6.13.0/30 [110/21] via 10.6.52.2, 00:26:50, FastEthernet0/0
C       10.0.3.0/24 is directly connected, FastEthernet0/1
O IA    10.6.1.0/24 [110/40] via 10.6.52.2, 00:26:50, FastEthernet0/0
O IA    10.6.2.0/24 [110/40] via 10.6.52.2, 00:26:50, FastEthernet0/0
O IA    10.6.3.0/24 [110/21] via 10.6.52.2, 00:26:50, FastEthernet0/0
O IA    10.6.21.0/30 [110/20] via 10.6.52.2, 00:26:50, FastEthernet0/0
O IA    10.6.23.0/30 [110/20] via 10.6.52.2, 00:26:51, FastEthernet0/0
O IA    10.6.41.0/30 [110/30] via 10.6.52.2, 00:26:51, FastEthernet0/0
C       10.6.52.0/30 is directly connected, FastEthernet0/0
S*   0.0.0.0/0 [254/0] via 10.0.3.2

```


🌞 **Test**



```
waf.tp6.b1> ping 10.6.3.11

84 bytes from 10.6.3.11 icmp_seq=1 ttl=62 time=39.059 ms
84 bytes from 10.6.3.11 icmp_seq=2 ttl=62 time=32.617 ms
^C
waf.t
```

```
john.tp6.b1> ping 10.6.2.11

84 bytes from 10.6.2.11 icmp_seq=1 ttl=62 time=49.301 ms
84 bytes from 10.6.2.11 icmp_seq=2 ttl=62 time=37.212 ms
^C

```

```
meo.tp6.b1> ping 1.1.1.1

84 bytes from 1.1.1.1 icmp_seq=1 ttl=52 time=84.675 ms
84 bytes from 1.1.1.1 icmp_seq=2 ttl=52 time=86.778 ms
^C

```

```
[root@localhost ~]# ping google.com
PING google.com (216.58.215.46) 56(84) bytes of data.
64 bytes from par21s17-in-f14.1e100.net (216.58.215.46): icmp_seq=1 ttl=111 time=95.2 ms
64 bytes from par21s17-in-f14.1e100.net (216.58.215.46): icmp_seq=2 ttl=111 time=92.9 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 92.941/94.078/95.216/1.137 ms
```

🦈 **`tp6_ospf.pcapng`**

[OSPF](ospf.pcapng)

La destination du paquet est 224.0.0.5, c'est du multicast, ici un routeur va envoyer un paquet en multicast c'est à dire pour plusieurs personnes. 
## III. DHCP relay

➜ **DHCP Relay !**

🌞 **Configurer un serveur DHCP** sur `dhcp.tp6.b1`



```
[root@localhost dhcp]# sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
subnet 10.6.1.0 netmask 255.255.255.0 {
    range 10.6.1.100 10.6.1.200;
    option domain-name-servers 1.1.1.1;
    option routers 10.6.1.254;
}
subnet 10.6.3.0 netmask 255.255.255.0 {
    range 10.6.3.100 10.6.3.200;
    option domain-name-servers 1.1.1.1;
    option routers 10.6.3.254;
}
```

```
[root@localhost dhcp]# systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-12-05 16:05:28 CET; 1s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1287 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 4611)
     Memory: 4.6M
        CPU: 4ms
     CGroup: /system.slice/dhcpd.service
             └─1287 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Dec 05 16:05:28 localhost.localdomain dhcpd[1287]: ** Ignoring requests on enp0s8.  If this is not what
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]:    you want, please write a subnet declaration
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]:    in your dhcpd.conf file for the network segment
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]:    to which interface enp0s8 is attached. **
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]:
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]: Listening on LPF/enp0s3/08:00:27:71:e2:92/10.6.1.0/24
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]: Sending on   LPF/enp0s3/08:00:27:71:e2:92/10.6.1.0/24
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]: Sending on   Socket/fallback/fallback-net
Dec 05 16:05:28 localhost.localdomain dhcpd[1287]: Server starting service.
Dec 05 16:05:28 localhost.localdomain systemd[1]: Started DHCPv4 Server Daemon.
```

🌞 **Configurer un DHCP relay sur la passerelle de John**



```cisco
R1#conf t
R1(config)#interface fastEthernet 2/0 # interface qui va recevoir des requêtes DHCP
R1(config-if)#ip helper-address <DHCP_SERVER_IP_ADDRESS>
```

```
waf.tp6.b1> ip  dhcp
DDORA IP 10.6.1.100/24 GW 10.6.1.254

```

```
john.tp6.b1> ip dhcp
DDORA IP 10.6.3.100/24 GW 10.6.3.254

```
```
Dec 07 14:53:48 localhost.localdomain dhcpd[743]: DHCPDISCOVER from 00:50:79:66:68:01 via enp0s3
Dec 07 14:53:49 localhost.localdomain dhcpd[743]: DHCPOFFER on 10.6.1.100 to 00:50:79:66:68:01 (waf.tp6.b1) via enp0s3
Dec 07 14:53:52 localhost.localdomain dhcpd[743]: DHCPREQUEST for 10.6.1.100 (10.6.1.253) from 00:50:79:66:68:01 (waf.tp6.b1) via enp0s3
Dec 07 14:53:52 localhost.localdomain dhcpd[743]: DHCPACK on 10.6.1.100 to 00:50:79:66:68:01 (waf.tp6.b1) via enp0s3
Dec 07 14:53:58 localhost.localdomain dhcpd[743]: reuse_lease: lease age 1009 (secs) under 25% threshold, reply with unaltered, existing lease for 10.6.3.100
Dec 07 14:53:58 localhost.localdomain dhcpd[743]: DHCPDISCOVER from 00:50:79:66:68:00 (john.tp6.b1) via 10.6.3.254
Dec 07 14:53:58 localhost.localdomain dhcpd[743]: DHCPOFFER on 10.6.3.100 to 00:50:79:66:68:00 (john.tp6.b1) via 10.6.3.254
Dec 07 14:53:59 localhost.localdomain dhcpd[743]: reuse_lease: lease age 1010 (secs) under 25% threshold, reply with unaltered, existing lease for 10.6.3.100
Dec 07 14:53:59 localhost.localdomain dhcpd[743]: DHCPREQUEST for 10.6.3.100 (10.6.1.253) from 00:50:79:66:68:00 (john.tp6.b1) via 10.6.3.254
Dec 07 14:53:59 localhost.localdomain dhcpd[743]: DHCPACK on 10.6.3.100 to 00:50:79:66:68:00 (john.tp6.b1) via 10.6.3.254
```