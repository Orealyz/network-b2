# TP8 INFRA : Make ur own

## Sommaire

- [TP8 INFRA : Make ur own](#tp8-infra--make-ur-own)
  - [Sommaire](#sommaire)
  - [I. Expression du besoin](#i-expression-du-besoin)
  - [II. Rendu attendu](#ii-rendu-attendu)

## I. Expression du besoin

## II. Rendu attendu

🌞 **Rendu Markdown**

🌞 **Schéma réseau**
Si il y a probleme avec le pdf, hésite pas à m'envoyer un message.
[tableau d'adressage](gns3.pdf)
[shéma physique](./schéma.png)
[schéma logique](./topologie.png)

🌞 **Tableaux d'adressage et VLAN**


🌞 **Config de toutes les machines**


Au vue de la demande, je vous propose un pour le site Meow Origins une architecture type 3-tier architecture qui va permettre au vue du nombre de clients dans le réseau, une certaine sécurité, de la redondance et résilience. Si un jour, il y a un problème avec un opérateur vous pourrez toujours avoir accés à internet grâce à l'autre opérateur. Mais aussi si il y a des problèmes de cables etc. Tous les clients pourront toujours communiqués.
Du coté, du site Meow and Beyond, j'ai opté pour une architecture type router-on-a-stick, au vue d'une quantité plus restreint de clients cela permet d'avoir un plus faible coût pour votre entreprise , mais aussi, j'ai fais en sorte qu'elle puisse accueillir de nouveaux clients si votre entreprise venez à se développer. Les deux réseaux peuvent se ping. 

```
PC1> ping 10.1.4.1

84 bytes from 10.1.4.1 icmp_seq=1 ttl=63 time=29.253 ms
84 bytes from 10.1.4.1 icmp_seq=2 ttl=63 time=15.728 ms
^C
  
```

```
PC5> ping  10.2.3.1

84 bytes from 10.2.3.1 icmp_seq=1 ttl=63 time=12.474 ms
84 bytes from 10.2.3.1 icmp_seq=2 ttl=63 time=16.070 ms
^C

```

```
PC1> ping google.com
google.com resolved to 142.250.179.110

google.com icmp_seq=1 timeout
^C

```

```
PC5> ping 10.1.2.1

84 bytes from 10.1.2.1 icmp_seq=1 ttl=61 time=45.734 ms
84 bytes from 10.1.2.1 icmp_seq=2 ttl=61 time=48.781 ms

```

```
PC2> ping 10.2.4.1

84 bytes from 10.2.4.1 icmp_seq=1 ttl=62 time=53.451 ms
84 bytes from 10.2.4.1 icmp_seq=2 ttl=62 time=53.723 ms

```

```
R1#sh run

interface FastEthernet0/0.2
 encapsulation dot1Q 2
 ip address 10.1.2.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 1 ip 10.1.2.254
 standby 1 priority 150
 standby 1 preempt
!
interface FastEthernet0/0.4
 encapsulation dot1Q 4
 ip address 10.1.4.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 4 ip 10.1.4.254
 standby 4 priority 150
 standby 4 preempt
!
interface FastEthernet0/0.5
 encapsulation dot1Q 5
 ip address 10.1.5.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 5 ip 10.1.5.254
 standby 5 priority 150
 standby 5 preempt
!
interface FastEthernet0/0.6
 encapsulation dot1Q 6
 ip address 10.1.6.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 6 ip 10.1.6.254
 standby 6 priority 150
 standby 6 preempt
!
interface FastEthernet0/0.7
 encapsulation dot1Q 7
 ip address 10.1.7.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 7 ip 10.1.7.254
 standby 7 priority 150
 standby 7 preempt
!
interface FastEthernet0/0.8
 encapsulation dot1Q 8
 ip address 10.1.8.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 8 ip 10.1.8.254
 standby 8 priority 150
 standby 8 preempt
!
interface FastEthernet0/0.9
 encapsulation dot1Q 9
 ip address 10.1.9.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 9 ip 10.1.9.254
 standby 9 priority 150
 standby 9 preempt
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 10 ip 10.1.10.254
 standby 10 priority 150
 standby 10 preempt
!
interface FastEthernet0/0.11
 encapsulation dot1Q 11
 ip address 10.1.11.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 11 ip 10.1.11.254
 standby 11 priority 150
 standby 11 preempt
!
interface FastEthernet0/0.12
 encapsulation dot1Q 12
 ip address 10.1.12.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 12 ip 10.1.12.254
 standby 12 priority 150
 standby 12 preempt
!
interface FastEthernet0/0.14
 encapsulation dot1Q 14
 ip address 10.1.14.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 14 ip 10.1.14.254
 standby 14 priority 150
 standby 14 preempt
!
interface FastEthernet0/0.15
 encapsulation dot1Q 15
 ip address 10.1.15.252 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 15 ip 10.1.15.254
 standby 15 priority 150
 standby 15 preempt
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
ip route 10.2.0.0 255.255.255.0 10.2.2.254
ip route 10.2.2.0 255.255.255.0 10.2.2.254
ip route 10.2.2.0 255.255.255.0 10.1.2.253
ip route 10.2.3.0 255.255.255.0 10.1.2.253
ip route 10.2.3.0 255.255.255.0 10.2.2.254
ip route 10.2.4.0 255.255.255.0 10.1.2.253
ip route 10.2.4.0 255.255.255.0 10.2.2.254
ip route 10.2.5.0 255.255.255.0 10.1.2.253
ip route 10.2.5.0 255.255.255.0 10.2.2.254
ip route 10.2.6.0 255.255.255.0 10.1.2.253
ip route 10.2.6.0 255.255.255.0 10.2.2.254
ip route 10.2.7.0 255.255.255.0 10.1.2.253
ip route 10.2.7.0 255.255.255.0 10.2.2.254
ip route 10.2.8.0 255.255.255.0 10.1.2.253
ip route 10.2.8.0 255.255.255.0 10.2.2.254
ip route 10.2.9.0 255.255.255.0 10.1.2.253
ip route 10.2.9.0 255.255.255.0 10.2.2.254
ip route 10.2.10.0 255.255.255.0 10.1.2.253
ip route 10.2.10.0 255.255.255.0 10.2.2.254
ip route 10.2.11.0 255.255.255.0 10.1.2.253
ip route 10.2.11.0 255.255.255.0 10.2.2.254
ip route 10.2.12.0 255.255.255.0 10.1.2.253
ip route 10.2.12.0 255.255.255.0 10.2.2.254
ip route 10.2.13.0 255.255.255.0 10.1.2.253
ip route 10.2.13.0 255.255.255.0 10.2.2.254
ip route 10.2.14.0 255.255.255.0 10.1.2.253
ip route 10.2.14.0 255.255.255.0 10.2.2.254
ip route 10.2.15.0 255.255.255.0 10.1.2.253
ip route 10.2.15.0 255.255.255.0 10.2.2.254

ip nat inside source list 1 interface FastEthernet0/1 overload
!
access-list 1 permit any

```

```
R2#sh run
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.2
 encapsulation dot1Q 2
 ip address 10.1.2.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 1 ip 10.1.2.254
!
interface FastEthernet0/0.4
 encapsulation dot1Q 4
 ip address 10.1.4.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 4 ip 10.1.4.254
!
interface FastEthernet0/0.5
 encapsulation dot1Q 5
 ip address 10.1.5.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 5 ip 10.1.5.254
!
interface FastEthernet0/0.6
 encapsulation dot1Q 6
 ip address 10.1.6.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 6 ip 10.1.6.254
!
interface FastEthernet0/0.7
 encapsulation dot1Q 7
 ip address 10.1.7.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 7 ip 10.1.7.254
!
interface FastEthernet0/0.8
 encapsulation dot1Q 8
 ip address 10.1.8.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 8 ip 10.1.8.254
!
interface FastEthernet0/0.9
 encapsulation dot1Q 9
 ip address 10.1.9.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 9 ip 10.1.9.254
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.1.10.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 10 ip 10.1.10.254
!
interface FastEthernet0/0.11
 encapsulation dot1Q 11
 ip address 10.1.11.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 11 ip 10.1.11.254
!
interface FastEthernet0/0.12
 encapsulation dot1Q 12
 ip address 10.1.12.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 12 ip 10.1.12.254
!
interface FastEthernet0/0.14
 encapsulation dot1Q 14
 ip address 10.1.14.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 14 ip 10.1.14.254
!
interface FastEthernet0/0.15
 encapsulation dot1Q 15
 ip address 10.1.15.253 255.255.255.0
 ip nat inside
 ip virtual-reassembly
 standby 15 ip 10.1.15.254
!
interface FastEthernet0/1
 ip address dhcp
 ip nat outside
 ip virtual-reassembly
 duplex auto
 speed auto
!
ip route 10.2.2.0 255.255.255.0 10.2.2.254
ip route 10.2.2.0 255.255.255.0 10.12.0.1
ip route 10.2.3.0 255.255.255.0 10.2.3.254
ip route 10.2.3.0 255.255.255.0 10.12.0.1
ip route 10.2.4.0 255.255.255.0 10.12.0.1
ip route 10.2.5.0 255.255.255.0 10.12.0.1
ip route 10.2.6.0 255.255.255.0 10.12.0.1
ip route 10.2.7.0 255.255.255.0 10.12.0.1
ip route 10.2.8.0 255.255.255.0 10.12.0.1
ip route 10.2.9.0 255.255.255.0 10.12.0.1
ip route 10.2.10.0 255.255.255.0 10.12.0.1
ip route 10.2.11.0 255.255.255.0 10.12.0.1
ip route 10.2.12.0 255.255.255.0 10.12.0.1
ip route 10.2.13.0 255.255.255.0 10.12.0.1
ip route 10.2.14.0 255.255.255.0 10.12.0.1
ip route 10.2.15.0 255.255.255.0 10.12.0.1

ip nat inside source list 1 interface FastEthernet0/1 overload
!
access-list 1 permit any

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
interface Ethernet1/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
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
interface Ethernet1/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
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
!
interface Ethernet1/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!

```

```
IOU5#sh run
interface Ethernet0/0
 switchport access vlan 2
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

Pour les switchs la configuration est la meme ensuite.



```

R3#sh run
interface FastEthernet0/0
 no ip address
 duplex auto
 speed auto
!
interface FastEthernet0/0.2
 encapsulation dot1Q 2
 ip address 10.2.2.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.3
 encapsulation dot1Q 3
 ip address 10.2.3.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.4
 encapsulation dot1Q 4
 ip address 10.2.4.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.5
 encapsulation dot1Q 5
 ip address 10.2.5.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.6
 encapsulation dot1Q 6
 ip address 10.2.6.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.7
 encapsulation dot1Q 7
 ip address 10.2.7.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.8
 encapsulation dot1Q 8
 ip address 10.2.8.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.9
 encapsulation dot1Q 9
 ip address 10.2.9.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 10.2.10.254 255.255.255.0
 ip nat inside
 ip virtual-reassembly
!
interface FastEthernet0/0.11
 encapsulation dot1Q 11
 ip address 10.2.11.254 255.255.255.0
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

ip route 10.1.2.0 255.255.255.0 10.1.2.254
ip route 10.1.2.0 255.255.255.0 10.1.2.253
ip route 10.1.2.0 255.255.255.0 10.12.0.2
ip route 10.1.3.0 255.255.255.0 10.12.0.2
ip route 10.1.4.0 255.255.255.0 10.12.0.2
ip route 10.1.5.0 255.255.255.0 10.12.0.2
ip route 10.1.6.0 255.255.255.0 10.12.0.2
ip route 10.1.7.0 255.255.255.0 10.12.0.2
ip route 10.1.8.0 255.255.255.0 10.12.0.2
ip route 10.1.9.0 255.255.255.0 10.12.0.2
ip route 10.1.10.0 255.255.255.0 10.12.0.2
ip route 10.1.11.0 255.255.255.0 10.12.0.2
ip route 10.1.12.0 255.255.255.0 10.12.0.2
ip route 10.1.13.0 255.255.255.0 10.12.0.2
ip route 10.1.14.0 255.255.255.0 10.12.0.2
ip route 10.1.15.0 255.255.255.0 10.12.0.2
ip route 10.2.2.0 255.255.255.0 10.12.0.2
ip route 10.2.3.0 255.255.255.0 10.12.0.2
ip route 10.2.4.0 255.255.255.0 10.12.0.2
ip route 10.2.5.0 255.255.255.0 10.12.0.2
ip route 10.2.6.0 255.255.255.0 10.12.0.2
ip route 10.2.7.0 255.255.255.0 10.12.0.2
ip route 10.2.8.0 255.255.255.0 10.12.0.2
ip route 10.2.9.0 255.255.255.0 10.12.0.2
ip route 10.2.10.0 255.255.255.0 10.12.0.2
ip route 10.2.11.0 255.255.255.0 10.12.0.2
ip route 10.2.12.0 255.255.255.0 10.12.0.2
ip route 10.2.13.0 255.255.255.0 10.12.0.2
ip route 10.2.14.0 255.255.255.0 10.12.0.2
ip route 10.2.15.0 255.255.255.0 10.12.0.2

!

ip nat inside source list 1 interface FastEthernet0/1 overload
!
access-list 1 permit any
```

```
sw1#sh run

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
 duplex half
!

```

```
sw2#sh run
interface Ethernet0/0
 switchport access vlan 2
 switchport mode access
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!

```

```
sw3#sh run

interface Ethernet0/0
 switchport access vlan 3
 switchport mode access
!
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk

```