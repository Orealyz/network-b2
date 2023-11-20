# II. NAT

## 1. Topologie 2

## 2. Adressage topologie 2

## 3. Setup topologie 2

🌞 **Ajoutez le noeud Cloud à la topo**


```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface FastEthernet0/1
R1(config-if)#ip address dhcp
R1(config-if)#no shut

```

```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES NVRAM  up                    up  
FastEthernet0/0.10         10.1.10.254     YES NVRAM  up                    up  
FastEthernet0/0.20         10.1.20.254     YES NVRAM  up                    up  
FastEthernet0/0.30         10.1.30.254     YES NVRAM  up                    up  
FastEthernet0/1            10.0.3.16       YES DHCP   up                    up  
FastEthernet1/0            unassigned      YES NVRAM  administratively down down
FastEthernet2/0            unassigned      YES NVRAM  administratively down down
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 20/20/24 ms

```
🌞 **Configurez le NAT**

```
conf t
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.254 255.255.255.0
 ip nat inside
exit
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.254 255.255.255.0
 ip nat inside
exit
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.1.30.254 255.255.255.0
exit
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
exit
access-list 1 permit any
ip nat inside source list 1 interface FastEthernet0/1 overload

```

```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface FastEthernet0/1
R1(config-if)#ip nat outside

```

```
R1(config)#interface fastEthernet 0/0.10
R1(config-subif)#ip nat inside
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.20
R1(config-subif)#ip nat inside
R1(config-subif)#exit
R1(config)#interface fastEthernet 0/0.30
R1(config-subif)#ip nat inside
R1(config-subif)#exit
R1(config)#access-list 1 permit any
R1(config)#ip nat inside source list 1 interface FastEthernet 0/1 overload

```

🌞 **Test**

La route est déjà activé grâce à la première topologie.



````
PC1> ip dns 8.8.8.8

PC1> ping google.com
google.com resolved to 142.250.201.14

google.com icmp_seq=1 timeout
google.com icmp_seq=2 timeout
^C

```

```
PC2> ip dns 8.8.8.8

PC2> ping google.com
google.com resolved to 142.250.201.14

google.com icmp_seq=1 timeout
google.com icmp_seq=2 timeout

```

```
adm1> ip dns 8.8.8.8

adm1> ping google.com
google.com resolved to 216.58.198.78

google.com icmp_seq=1 timeout

```

```
[root@localhost network-scripts]# cat ifcfg-enp0s3
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=static
ONBOOT=yes

IPADDR=10.1.30.1
NETMASK=255.255.255.0

GATEWAY=10.1.30.254
DNS1=8.8.8.8
```

```
[root@localhost ~]# ping google.com
PING google.com (142.250.201.14) 56(84) bytes of data.
64 bytes from mrs08s19-in-f14.1e100.net (142.250.201.14): icmp_seq=2 ttl=55 time=24.3 ms
^C64 bytes from 142.250.201.14: icmp_seq=3 ttl=55 time=27.8 ms

--- google.com ping statistics ---
3 packets transmitted, 2 received, 33.3333% packet loss, time 2011ms
rtt min/avg/max/mdev = 24.273/26.011/27.750/1.738 ms
```