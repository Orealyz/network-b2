# TP7 INFRA : 3-tier architecture et redondance


## Sommaire

- [TP7 INFRA : 3-tier architecture et redondance](#tp7-infra--3-tier-architecture-et-redondance)
  - [Sommaire](#sommaire)
    - [E. Preuve et rendu](#e-preuve-et-rendu)

### E. Preuve et rendu

🌞 **`show-run`** sur tous les équipements

J'ai essayé de faire cours pour les sh runs :)

```
R1#sh run

interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.7.10.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 10 ip 10.7.10.254
 standby 10 priority 150
 standby 10 preempt
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.7.20.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 20 ip 10.7.20.254
 standby 20 priority 150
 standby 20 preempt
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.7.30.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 30 ip 10.7.30.254
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!

ip nat inside source list 1 interface FastEthernet0/1 overload
!
access-list 1 permit any

```

```
R2#sh run

interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.7.10.253 255.255.255.0
 standby 10 ip 10.7.10.254
!
interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 10.7.20.253 255.255.255.0
 standby 20 ip 10.7.20.254
!
interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 10.7.30.253 255.255.255.0
 standby 30 ip 10.7.30.254
 standby 30 priority 150
 standby 30 preempt
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
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

```

```

IOU1#sh run

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode on
!
interface Ethernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode on
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 duplex half
!

```

```

IOU2#sh run

interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode on
!
interface Ethernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
 channel-group 1 mode on
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 duplex half
!

```

```
IOU3#sh run

interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk

```

```
IOU4#sh run

interface Ethernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet1/0
 switchport trunk encapsulation dot1q
 switchport mode trunk

```

```
IOU5#sh run

interface Ethernet0/0
 switchport access vlan 10
 switchport mode access
!
interface Ethernet0/1
 switchport access vlan 20
 switchport mode access
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/3
 switchport trunk encapsulation dot1q
 switchport mode trunk

```

```
IOU6#sh run

interface Ethernet0/0
 switchport access vlan 20
 switchport mode access
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
```

```
IOU7#sh run

interface Ethernet0/0
 switchport access vlan 30
 switchport trunk allowed vlan 1-9,11-19,21-4094
 switchport trunk encapsulation dot1q
 switchport mode access
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface Ethernet0/2
 switchport trunk encapsulation dot1q
 switchport mode trunk
!

```

🌞 **depuis `pc4.tp7.b1`**

- `ping 10.7.10.12`
- `ping ynov.com`

```
PC4> ping 10.7.20.11

84 bytes from 10.7.20.11 icmp_seq=1 ttl=63 time=17.428 ms
84 bytes from 10.7.20.11 icmp_seq=2 ttl=63 time=16.704 ms
^C
PC4> ping ynov.com
ynov.com resolved to 104.26.11.233

ynov.com icmp_seq=1 timeout
^C

```
