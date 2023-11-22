# III. Add a building

## 1. Topologie 3

## 2. Adressage topologie 3

## 3. Setup topologie 3

🌞  **Vous devez me rendre le `show running-config` de tous les équipements**

```
R1#show running-config
Building configuration...

Current configuration : 1709 bytes
!
version 12.4
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
!
hostname R1
!
boot-start-marker
boot-end-marker
!

no aaa new-model
memory-size iomem 5
no ip icmp rate-limit unreachable
ip cef
!
ip name-server 8.8.8.8
ip name-server 8.8.4.4
!
multilink bundle-name authenticated
!
archive
 log config
  hidekeys

!
ip tcp synwait-time 5

!
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.1.20.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.1.30.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
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
ip forward-protocol nd
!

no ip http server
no ip http secure-server
ip nat inside source list 1 interface FastEthernet0/1 overload
!
access-list 1 permit any
access-list 1 permit 10.1.10.0 0.0.0.255
access-list 1 permit 10.1.20.0 0.0.0.255
access-list 1 permit 10.1.30.0 0.0.0.255
no cdp log mismatch duplex

control-plane

line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
!
end

```


```
sw1#show running-config
Building configuration...

Current configuration : 1690 bytes
!
! Last configuration change at 21:09:38 UTC Wed Nov 22 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw1
!
boot-start-marker
boot-end-marker
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
no ip icmp rate-limit unreachable
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
!
!
spanning-tree mode pvst
spanning-tree extend system-id
!

interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 20
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 30
 switchport mode access
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!

control-plane
!
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
```

```
sw2#show running-config
Building configuration...

Current configuration : 1690 bytes
!
! Last configuration change at 21:09:38 UTC Wed Nov 22 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw2
!
boot-start-marker
boot-end-marker
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
no ip icmp rate-limit unreachable
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 20
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 30
 switchport mode access
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
control-plane
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
end

```

```
sw3#show running-config
Building configuration...

Current configuration : 1690 bytes
!
! Last configuration change at 21:09:38 UTC Wed Nov 22 2023
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!
hostname sw3
!
boot-start-marker
boot-end-marker
!
logging discriminator EXCESS severity drops 6 msg-body drops EXCESSCOLL
logging buffered 50000
logging console discriminator EXCESS
!
no aaa new-model
!
no ip icmp rate-limit unreachable
!
no ip domain-lookup
no ip cef
no ipv6 cef
!
spanning-tree mode pvst
spanning-tree extend system-id
!
interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/2
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/3
 switchport access vlan 10
 switchport mode access
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/1
!
interface Ethernet1/2
!
interface Ethernet1/3
!
interface Ethernet2/0
!
interface Ethernet2/1
!
interface Ethernet2/2
!
interface Ethernet2/3
!
interface Ethernet3/0
!
interface Ethernet3/1
!
interface Ethernet3/2
!
interface Ethernet3/3
!
interface Vlan1
 no ip address
 shutdown
!
ip forward-protocol nd
!
ip tcp synwait-time 5
ip http server
!
ip ssh server algorithm encryption aes128-ctr aes192-ctr aes256-ctr
ip ssh client algorithm encryption aes128-ctr aes192-ctr aes256-ctr
!
control-plane
!
line con 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line aux 0
 exec-timeout 0 0
 privilege level 15
 logging synchronous
line vty 0 4
 login
!
end

```
🌞  **Mettre en place un serveur DHCP dans le nouveau bâtiment**

```
[root@localhost ~]# cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
subnet 10.1.10.0 netmask 255.255.255.0 {
    range 10.1.10.100 10.1.10.200;
    option domain-name-servers 8.8.8.8;
    option routers 10.1.10.254;
}
```

🌞  **Vérification**

Toujours le meme problème vue avec toi je ne peux pas ping depuis un vpcs1 mais cela fonctionne sur une vm

```
[root@localhost ~]# ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
^C
--- 1.1.1.1 ping statistics ---
1 packets transmitted, 0 received, 100% packet loss, time 0ms

[root@localhost ~]# ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=2 ttl=54 time=24.6 ms
^C
--- 1.1.1.1 ping statistics ---
2 packets transmitted, 1 received, 50% packet loss, time 1031ms
rtt min/avg/max/mdev = 24.603/24.603/24.603/0.000 ms
[root@localhost ~]# ping ynov.com
PING ynov.com (104.26.10.233) 56(84) bytes of data.
64 bytes from 104.26.10.233 (104.26.10.233): icmp_seq=2 ttl=54 time=25.6 ms
^C
--- ynov.com ping statistics ---
2 packets transmitted, 1 received, 50% packet loss, time 1033ms
rtt min/avg/max/mdev = 25.596/25.596/25.596/0.000 ms
```

```
PC3> ip dhcp
DDORA IP 10.1.10.101/24 GW 10.1.10.254

PC3> show ip

NAME        : PC3[1]
IP/MASK     : 10.1.10.101/24
GATEWAY     : 10.1.10.254
DNS         : 8.8.8.8
DHCP SERVER : 10.1.10.253
DHCP LEASE  : 43193, 43200/21600/37800
MAC         : 00:50:79:66:68:02
LPORT       : 20030
RHOST:PORT  : 127.0.0.1:20031
MTU         : 1500

PC3> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=8.632 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=2.465 ms
^C
PC3> ping 1.1.1.1

1.1.1.1 icmp_seq=1 timeout
^C
PC3> ping ynov.com
ynov.com resolved to 104.26.10.233

ynov.com icmp_seq=1 timeout
ynov.com icmp_seq=2 timeout
^C

```