# I. Topo 1 : VLAN et Routing

## 1. Topologie 1

## 2. Adressage topologie 1

## 3. Setup topologie 1

🌞 **Adressage**

```
PC1> ip 10.1.10.1
Checking for duplicate address...
PC1 : 10.1.10.1 255.255.255.0

PC2> ip 10.1.10.2
Checking for duplicate address...
PC2 : 10.1.10.2 255.255.255.0

adm1> ip 10.1.20.1
Checking for duplicate address...
adm1 : 10.1.20.1 255.255.255.0

[root@localhost ~]# ip a
$1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:71:e2:92 brd ff:ff:ff:ff:ff:ff
    inet 10.1.30.1/24 brd 10.1.30.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe71:e292/64 scope link
       valid_lft forever preferred_lft forever
```


🌞 **Configuration des VLANs**


```
sw1#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Et1/0, Et1/1, Et1/2, Et1/3
                                                Et2/0, Et2/1, Et2/2, Et2/3
                                                Et3/0, Et3/1, Et3/2, Et3/3
10   clients                          active    Et0/0, Et0/1
20   admins                           active    Et0/2
30   servers                          active    Et0/3

```
---

➜ **Pour le *routeur***


🌞 **Config du *routeur***

- attribuez ses IPs au *routeur*
  - 3 sous-interfaces, chacune avec son IP et un VLAN associé

```
R1#show ip interface br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES NVRAM  up                    up
FastEthernet0/0.10         10.1.10.254     YES NVRAM  up                    up
FastEthernet0/0.20         10.1.20.254     YES NVRAM  up                    up
FastEthernet0/0.30         10.1.30.254     YES NVRAM  up                    up
[...]
```

🌞 **Vérif**

```
PC1> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=9.374 ms
84 bytes from 10.1.10.254 icmp_seq=2 ttl=255 time=6.807 ms

```

```
PC2> ping 10.1.10.254

84 bytes from 10.1.10.254 icmp_seq=1 ttl=255 time=9.110 ms
^C

```

```
adm1> ping 10.1.20.254

84 bytes from 10.1.20.254 icmp_seq=1 ttl=255 time=10.305 ms
^C

```

```
[root@localhost ~]# ping 10.1.30.254
PING 10.1.30.254 (10.1.30.254) 56(84) bytes of data.
64 bytes from 10.1.30.254: icmp_seq=1 ttl=255 time=18.1 ms
^C
--- 10.1.30.254 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 18.090/18.090/18.090/0.000 ms
```

```
PC1> ip  10.1.10.1 255.255.255.0 10.1.10.254
Checking for duplicate address...
PC1 : 10.1.10.1 255.255.255.0 gateway 10.1.10.254

```

```
adm1> ip  10.1.20.1 255.255.255.0 10.1.20.254
Checking for duplicate address...
adm1 : 10.1.20.1 255.255.255.0 gateway 10.1.20.254

```

```
PC2> ip  10.1.10.2 255.255.255.0 10.1.10.254
Checking for duplicate address...
PC2 : 10.1.10.2 255.255.255.0 gateway 10.1.10.254

```

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
```
```

```
PC2> ping 10.1.20.1

84 bytes from 10.1.20.1 icmp_seq=1 ttl=63 time=19.794 ms
```

```
adm1> ping 10.1.10.1

84 bytes from 10.1.10.1 icmp_seq=1 ttl=63 time=28.828 ms
^C
```
```
PC1> ping 10.1.30.1

84 bytes from 10.1.30.1 icmp_seq=1 ttl=63 time=20.193 ms
^C

```

```
adm1> ping 10.1.30.1

84 bytes from 10.1.30.1 icmp_seq=1 ttl=63 time=20.147 ms
^C

```

```
[root@localhost network-scripts]# ping 10.1.10.1
PING 10.1.10.1 (10.1.10.1) 56(84) bytes of data.
64 bytes from 10.1.10.1: icmp_seq=1 ttl=63 time=31.1 ms
^C
--- 10.1.10.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 31.107/31.107/31.107/0.000 ms
```
