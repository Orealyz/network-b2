# TP3 INFRA : Premiers pas GNS, Cisco et VLAN

# Sommaire

- [TP3 INFRA : Premiers pas GNS, Cisco et VLAN](#tp3-infra--premiers-pas-gns-cisco-et-vlan)
- [Sommaire](#sommaire)
- [I. Dumb switch](#i-dumb-switch)
  - [1. Topologie 1](#1-topologie-1)
  - [2. Adressage topologie 1](#2-adressage-topologie-1)
  - [3. Setup topologie 1](#3-setup-topologie-1)
- [II. VLAN](#ii-vlan)
  - [1. Topologie 2](#1-topologie-2)
  - [2. Adressage topologie 2](#2-adressage-topologie-2)
    - [3. Setup topologie 2](#3-setup-topologie-2)
- [III. Ptite VM DHCP](#iii-ptite-vm-dhcp)


# I. Dumb switch

## 1. Topologie 1

## 2. Adressage topologie 1

## 3. Setup topologie 1

🌞 **Commençons simple**


```
PC2> ip 10.3.1.2
Checking for duplicate address...
PC2 : 10.3.1.2 255.255.255.0

PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=0.275 ms
^C

```
```
PC1> ip 10.3.1.1
Checking for duplicate address...
PC1 : 10.3.1.1 255.255.255.0

PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.162 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=0.229 ms
^C

```
```
IOU1#show mac address-table
          Mac Address Table
-------------------------------------------

Vlan    Mac Address       Type        Ports
----    -----------       --------    -----

```


# II. VLAN

## 1. Topologie 2

## 2. Adressage topologie 2

### 3. Setup topologie 2

🌞 **Adressage**


j'ai gardé les VPCS de la première topologie et leurs confs. 

```
PC3> ip 10.3.1.3
Checking for duplicate address...
PC3 : 10.3.1.3 255.255.255.0

```

```
PC3> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=0.147 ms
^C
PC3> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.181 ms
84 bytes from 10.3.1.2 icmp_seq=2 ttl=64 time=0.250 ms
^C

```

```
PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=0.138 ms
^X84 bytes from 10.3.1.1 icmp_seq=2 ttl=64 time=0.184 ms
^C
PC2>
PC2> ping 10.3.1.3

84 bytes from 10.3.1.3 icmp_seq=1 ttl=64 time=0.234 ms
^C

```

```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.308 ms
^C
PC1> ping 10.3.1.3

84 bytes from 10.3.1.3 icmp_seq=1 ttl=64 time=0.235 ms
^C

```

🌞 **Configuration des VLANs**


```
sw1(config)#vlan 20
sw1(config-vlan)#name admins
sw1(config-vlan)#exit

sw1(config)#vlan 10
sw1(config-vlan)#name guests
sw1(config-vlan)#exit

```
```
sw1(config)#interface Ethernet0/0
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 10

sw1(config)#interface Ethernet0/1
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 10

sw1(config)#interface Ethernet0/2
sw1(config-if)#switchport mode access
sw1(config-if)#switchport access vlan 20

```
```
sw1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et0/3, Et1/0, Et1/1, Et1/2
                                                Et1/3, Et2/0, Et2/1, Et2/2
                                                Et2/3, Et3/0, Et3/1, Et3/2
                                                Et3/3
10   guests                           active    Et0/0, Et0/1
20   admins                           active    Et0/2
[...]
```
🌞 **Vérif**



```
PC1> ping 10.3.1.2

84 bytes from 10.3.1.2 icmp_seq=1 ttl=64 time=0.801 ms
^C
PC1> ping 10.3.1.3

host (10.3.1.3) not reachable

```

```
PC2> ping 10.3.1.1

84 bytes from 10.3.1.1 icmp_seq=1 ttl=64 time=0.212 ms
^C
PC2> ping 10.3.1.3

host (10.3.1.3) not reachable

```

```
PC3> ping 10.3.1.1

host (10.3.1.1) not reachable

PC3> ping 10.3.1.2

host (10.3.1.2) not reachable

```


# III. Ptite VM DHCP

🌞 **VM `dhcp.tp3.b2`**


```
IOU1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et1/2, Et1/3, Et2/0, Et2/1
                                                Et2/2, Et2/3, Et3/0, Et3/1
                                                Et3/2, Et3/3
10   guests                           active    Et0/0, Et0/1, Et1/0
20   admins                           active    Et0/2, Et0/3, Et1/1
1002 fddi-default                     act/unsup
1003 token-ring-default               act/unsup
1004 fddinet-default                  act/unsup
1005 trnet-default                    act/unsup

```

```
[root@localhost dhcp]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:71:e2:92 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.253/24 brd 10.3.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe71:e292/64 scope link
       valid_lft forever preferred_lft forever
```

```
[root@localhost ~]# dnf -y install dhcp-server
```

```
[root@localhost dhcp]# cat dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
subnet 10.3.1.0 netmask 255.255.255.0 {
    range 10.3.1.100 10.3.1.200;
    option domain-name-servers 8.8.8.8, 8.8.4.4;
    option routers 10.3.1.253;
}
```

```
PC4> ip dhcp
DDORA IP 10.3.1.100/24 GW 10.3.1.253

PC4> show ip

NAME        : PC4[1]
IP/MASK     : 10.3.1.100/24
GATEWAY     : 10.3.1.253
DNS         : 8.8.8.8  8.8.4.4
DHCP SERVER : 10.3.1.253
DHCP LEASE  : 43178, 43200/21600/37800
MAC         : 00:50:79:66:68:03
LPORT       : 20016
RHOST:PORT  : 127.0.0.1:20017
MTU         : 1500

```

```
PC5> ip dhcp
DDD
Can't find dhcp server

```

